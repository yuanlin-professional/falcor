# RenderPass 源码文档

> 路径: `Source/Falcor/RenderGraph/RenderPass.h` + `RenderPass.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderGraph (渲染图)

## 功能概述

定义了渲染通道的基类 `RenderPass` 和辅助类 `RenderData`。`RenderPass` 是所有渲染通道的抽象基类，定义了反射、编译、执行等核心接口。`RenderData` 是在 `execute()` 阶段传递给渲染通道的辅助对象，提供对资源缓存和全局字典的访问。

## 类与接口

### `RenderData`

- **继承**: 无
- **职责**: 在渲染通道执行时提供资源访问和元数据查询。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `const ref<Resource>& operator[](const std::string_view name) const` | 通过名称获取资源（操作符重载） |
| `const ref<Resource>& getResource(const std::string_view name) const` | 通过名称获取资源 |
| `ref<Texture> getTexture(const std::string_view name) const` | 通过名称获取纹理 |
| `Dictionary& getDictionary() const` | 获取全局字典，用于通道间数据传递 |
| `const uint2& getDefaultTextureDims() const` | 获取默认纹理尺寸 |
| `ResourceFormat getDefaultTextureFormat() const` | 获取默认纹理格式 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mName` | `const std::string&` | 渲染通道名称 |
| `mResources` | `ResourceCache&` | 资源缓存引用 |
| `mDictionary` | `Dictionary&` | 全局字典引用 |
| `mDefaultTexDims` | `uint2` | 默认纹理尺寸 |
| `mDefaultTexFormat` | `ResourceFormat` | 默认纹理格式 |

### `RenderPass`

- **继承**: `Object`
- **职责**: 渲染通道抽象基类，定义所有渲染通道的公共接口。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<RenderPass> create(std::string_view type, ref<Device>, const Properties&, PluginManager&)` | 通过插件系统创建指定类型的渲染通道 |
| `virtual RenderPassReflection reflect(const CompileData&) = 0` | **纯虚函数**。声明渲染通道的 I/O 需求 |
| `virtual void compile(RenderContext*, const CompileData&)` | 图编译阶段调用，可选重载 |
| `virtual void execute(RenderContext*, const RenderData&) = 0` | **纯虚函数**。每帧执行渲染通道 |
| `virtual void setProperties(const Properties&)` | 设置渲染通道属性 |
| `virtual Properties getProperties() const` | 获取渲染通道属性 |
| `virtual void renderUI(RenderContext*, Gui::Widgets&)` | 渲染通道 UI |
| `virtual void renderOverlayUI(RenderContext*)` | 渲染叠加层 UI |
| `virtual void setScene(RenderContext*, const ref<Scene>&)` | 设置场景 |
| `virtual void onSceneUpdates(RenderContext*, IScene::UpdateFlags)` | 场景更新回调 |
| `virtual bool onMouseEvent(const MouseEvent&)` | 鼠标事件处理 |
| `virtual bool onKeyEvent(const KeyboardEvent&)` | 键盘事件处理 |
| `virtual void onHotReload(HotReloadFlags)` | 热重载回调 |
| `const std::string& getName() const` | 获取通道在图中的名称 |
| `const std::string& getType() const` | 获取通道类型名 |
| `const std::string& getDesc() const` | 获取通道描述 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | GPU 设备引用 |
| `mName` | `std::string` | 通道在图中的名称 |
| `mPassChangedCB` | `std::function<void(void)>` | I/O 变更回调（触发重编译） |

### `CompileData`

| 成员 | 类型 | 说明 |
|------|------|------|
| `defaultTexDims` | `uint2` | 默认纹理尺寸（交换链大小） |
| `defaultTexFormat` | `ResourceFormat` | 默认纹理格式（交换链格式） |
| `connectedResources` | `RenderPassReflection` | 已连接资源的反射数据 |

## 依赖关系

### 本文件引用

- `ResourceCache.h` - 资源缓存
- `Core/Object.h` - 对象基类
- `Core/Plugin.h` - 插件系统
- `Core/API/Resource.h` / `Texture.h` - GPU 资源
- `Scene/Scene.h` - 场景
- `Utils/Properties.h` - 属性字典
- `Utils/Dictionary.h` - 全局字典

### 被以下文件引用

- `RenderGraph.h/cpp` - 渲染图
- `RenderGraphCompiler.h/cpp` - 编译器
- `RenderGraphExe.h/cpp` - 执行器
- `RenderGraphUI.h/cpp` - UI
- `RenderPassHelpers.h/cpp` - 辅助工具
- 所有具体的渲染通道实现

## 实现细节

- **插件系统**: `RenderPass::create()` 使用 `PluginManager` 动态加载渲染通道。如果类型未注册，会尝试加载同名插件。
- **反射机制**: `reflect()` 是纯虚函数，可被多次调用且不应执行昂贵操作。I/O 需求在图编译后不可变更，如需动态变更需调用 `requestRecompile()`。
- **资源访问**: `RenderData::getResource()` 通过 `passName.resourceName` 格式的全限定名从资源缓存中查找资源。
- **生命周期**: 渲染通道由 `RenderGraph` 持有，通过 `ref<>` 引用计数管理。
