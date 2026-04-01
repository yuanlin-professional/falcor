# ResourceCache 源码文档

> 路径: `Source/Falcor/RenderGraph/ResourceCache.h` + `ResourceCache.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderGraph (渲染图)

## 功能概述

`ResourceCache` 是渲染图的资源管理器，负责为渲染通道分配和缓存 GPU 资源（纹理和缓冲区）。它管理两类资源：由图内部分配的资源和外部注册的资源。支持资源别名（多个字段共享同一资源）和基于反射数据的自动资源创建。

## 类与接口

### `ResourceCache`

- **继承**: 无
- **职责**: 管理渲染图中所有字段的资源分配、别名和查询。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void registerExternalResource(const std::string& name, const ref<Resource>&)` | 注册或注销外部输入资源 |
| `void registerField(const std::string& name, const Field& field, uint32_t timePoint, const std::string& alias)` | 注册需要分配资源的字段。可指定别名以共享资源 |
| `const ref<Resource>& getResource(const std::string& name) const` | 按名称获取资源（先查找内部资源，再查找外部资源） |
| `const RenderPassReflection::Field& getResourceReflection(const std::string& name) const` | 获取资源的反射数据 |
| `void allocateResources(ref<Device>, const DefaultProperties&)` | 分配所有未创建的资源 |
| `void reset()` | 清除所有注册的字段和已分配的资源 |

### `DefaultProperties`

| 成员 | 类型 | 说明 |
|------|------|------|
| `dims` | `uint2` | 交换链的宽高 |
| `format` | `ResourceFormat` | 纹理创建的默认格式 |

### `ResourceData`（私有）

| 成员 | 类型 | 说明 |
|------|------|------|
| `field` | `RenderPassReflection::Field` | 合并后的资源属性 |
| `lifetime` | `std::pair<uint32_t, uint32_t>` | 资源使用的时间范围 |
| `pResource` | `ref<Resource>` | 实际的 GPU 资源 |
| `resolveBindFlags` | `bool` | 是否需要自动解析绑定标志 |
| `name` | `std::string` | 资源全名（含通道名） |

### 类型别名

| 名称 | 定义 | 说明 |
|------|------|------|
| `ResourcesMap` | `std::unordered_map<std::string, ref<Resource>>` | 资源名称到资源的映射 |

## 依赖关系

### 本文件引用

- `RenderPassReflection.h` - 字段反射数据
- `Core/API/Resource.h` - GPU 资源基类
- `Core/API/Device.h` - GPU 设备
- `Core/API/Texture.h` - 纹理
- `Core/API/Buffer.h` - 缓冲区

### 被以下文件引用

- `RenderPass.h` - 渲染通道基类
- `RenderGraphExe.h/cpp` - 执行器
- `RenderGraphCompiler.h/cpp` - 编译器

## 实现细节

- **资源查找优先级**: `getResource()` 先在内部资源 (`mResourceData`) 中查找，如果未找到再在外部资源 (`mExternalResources`) 中查找。
- **别名机制**: `registerField()` 的 `alias` 参数允许将新字段与已存在字段关联，共享同一资源。别名字段的属性会通过 `Field::merge()` 合并，生命周期也会扩展。
- **自动绑定标志解析**: 当字段的 `bindFlags` 为 `None` 时，`resolveBindFlags` 设为 true。资源创建时会根据字段可见性和设备支持自动推断绑定标志：
  - 所有资源：`UnorderedAccess | ShaderResource`
  - 输出/内部资源：额外添加 `DepthStencil | RenderTarget`（如格式支持）
- **资源创建**: `createResourceForPass()` 根据字段类型创建对应的 GPU 资源：
  - `RawBuffer` -> `createBuffer()`
  - `Texture1D` -> `createTexture1D()`
  - `Texture2D` -> `createTexture2D()` 或 `createTexture2DMS()`（多采样）
  - `Texture3D` -> `createTexture3D()`
  - `TextureCube` -> `createTextureCube()`
- **尺寸默认值**: 宽度和高度为 0 时使用 `DefaultProperties` 中的交换链尺寸；深度为 0 时默认为 1。
- **格式默认值**: 纹理格式为 `Unknown` 时使用默认格式；缓冲区不使用格式。
