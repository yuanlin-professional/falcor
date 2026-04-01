# RenderPassReflection 源码文档

> 路径: `Source/Falcor/RenderGraph/RenderPassReflection.h` + `RenderPassReflection.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderGraph (渲染图)

## 功能概述

`RenderPassReflection` 是渲染通道的 I/O 反射系统，用于描述渲染通道所需的输入、输出和内部资源。每个字段 (`Field`) 描述了一个资源的类型、尺寸、格式、采样数等属性。编译器使用反射数据来验证连接、分配资源和自动插入转换通道。

## 类与接口

### `RenderPassReflection`

- **继承**: 无
- **职责**: 管理渲染通道的字段（资源描述）集合，提供添加和查询字段的接口。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `Field& addInput(const std::string& name, const std::string& desc)` | 添加输入字段 |
| `Field& addOutput(const std::string& name, const std::string& desc)` | 添加输出字段 |
| `Field& addInputOutput(const std::string& name, const std::string& desc)` | 添加输入/输出双向字段 |
| `Field& addInternal(const std::string& name, const std::string& desc)` | 添加内部字段 |
| `Field& addField(const Field& field)` | 添加已构造的字段（如同名字段已存在则合并） |
| `size_t getFieldCount() const` | 获取字段数量 |
| `const Field* getField(size_t f) const` | 按索引获取字段 |
| `const Field* getField(const std::string& name) const` | 按名称获取字段（const） |
| `Field* getField(const std::string& name)` | 按名称获取字段（可修改） |
| `bool operator==(const RenderPassReflection&) const` | 比较两个反射对象是否相等 |

### `RenderPassReflection::Field`

- **继承**: 无
- **职责**: 描述单个资源字段的所有属性。

#### Visibility 枚举

| 枚举值 | 说明 |
|--------|------|
| `Undefined` | 未定义 |
| `Input` | 输入字段 |
| `Output` | 输出字段 |
| `Internal` | 内部字段（请求资源缓存分配内部资源） |

#### Flags 枚举

| 枚举值 | 说明 |
|--------|------|
| `None` | 无标志 |
| `Optional` | 可选字段。输出不必绑定除非调用者需要；输入可不绑定但行为可能不同 |
| `Persistent` | 持久字段。资源在 `execute()` 调用间保持不变，仅在重编译时变更 |

#### Type 枚举

| 枚举值 | 说明 |
|--------|------|
| `Texture1D` | 一维纹理 |
| `Texture2D` | 二维纹理 |
| `Texture3D` | 三维纹理 |
| `TextureCube` | 立方体纹理 |
| `RawBuffer` | 原始缓冲区 |

#### 关键方法（链式调用）

| 方法签名 | 说明 |
|----------|------|
| `Field& rawBuffer(uint32_t size)` | 设置为原始缓冲区类型 |
| `Field& texture1D(uint32_t width, uint32_t mipCount, uint32_t arraySize)` | 设置为 1D 纹理 |
| `Field& texture2D(uint32_t width, uint32_t height, uint32_t sampleCount, uint32_t mipCount, uint32_t arraySize)` | 设置为 2D 纹理 |
| `Field& texture3D(uint32_t width, uint32_t height, uint32_t depth, uint32_t arraySize)` | 设置为 3D 纹理 |
| `Field& textureCube(uint32_t width, uint32_t height, uint32_t mipCount, uint32_t arraySize)` | 设置为立方体纹理 |
| `Field& format(ResourceFormat)` | 设置资源格式 |
| `Field& bindFlags(ResourceBindFlags)` | 设置绑定标志 |
| `Field& flags(Flags)` | 设置字段标志 |
| `Field& visibility(Visibility)` | 设置可见性 |
| `Field& merge(const Field& other)` | 合并另一个字段的属性（用于别名资源） |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mName` | `std::string` | 字段名称 |
| `mDesc` | `std::string` | 字段描述 |
| `mType` | `Type` | 资源类型（默认 Texture2D） |
| `mWidth/mHeight/mDepth` | `uint32_t` | 尺寸（0 表示不关心） |
| `mSampleCount` | `uint32_t` | 采样数（默认 1） |
| `mMipCount` | `uint32_t` | Mip 层级数（默认 1） |
| `mArraySize` | `uint32_t` | 数组大小（默认 1） |
| `mFormat` | `ResourceFormat` | 资源格式（Unknown 表示自动决定） |
| `mBindFlags` | `ResourceBindFlags` | 资源绑定标志 |
| `mFlags` | `Flags` | 字段标志 |
| `mVisibility` | `Visibility` | 可见性 |

## 全局辅助函数

| 函数签名 | 说明 |
|----------|------|
| `std::string to_string(Field::Type)` | 字段类型转字符串 |
| `Field::Type resourceTypeToFieldType(Resource::Type)` | Resource 类型转 Field 类型 |

## 依赖关系

### 本文件引用

- `Core/API/Formats.h` - 资源格式
- `Core/API/Resource.h` - GPU 资源
- `Core/API/Texture.h` - 纹理

### 被以下文件引用

- `RenderPass.h` - 渲染通道基类
- `RenderGraphCompiler.cpp` - 编译器
- `ResourceCache.h/cpp` - 资源缓存
- `RenderPassHelpers.h` - 辅助工具
- `RenderGraphUI.h` - UI

## 实现细节

- **链式 API**: `Field` 的设置方法返回自身引用，支持链式调用，如 `reflector.addOutput("color", "desc").texture2D(1920, 1080).format(RGBA32Float)`。
- **字段合并**: `merge()` 方法用于将别名资源的属性合并。对于未指定（值为 0）的属性采用对方的值；对于双方都指定但不一致的属性，抛出异常。合并不适用于 Internal 类型的字段。
- **有效性验证**: `isValid()` 检查多采样纹理的 mip 数不能大于 1，Internal 字段不能标记为 Optional。
- **字段查找/合并**: `addField()` 会查找同名字段，如果同时具有 Input 和 Output 可见性则合并，否则覆盖并发出警告。
- **相等比较**: 两个 `RenderPassReflection` 相等当且仅当字段数量相同且每个字段都能在对方找到匹配（与顺序无关）。
