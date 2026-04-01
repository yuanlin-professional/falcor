# GraphicsState 源码文档

> 路径: `Source/Falcor/Core/State/GraphicsState.h` + `Source/Falcor/Core/State/GraphicsState.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/State

## 功能概述

`GraphicsState` 封装了执行一次图形绘制调用（draw call）所需的全部管线状态。它是 Falcor 图形渲染管线的核心状态管理类，包含帧缓冲（FBO）、顶点数组对象（VAO）、着色器程序、混合状态、光栅化状态、深度模板状态、视口、裁剪矩形等。该类支持通过栈机制保存和恢复状态，便于多通道渲染。推荐为每个渲染通道创建一个独立的 `GraphicsState` 实例。

## 类与接口

### `GraphicsState`

- **继承**: `Object`（Falcor 引用计数基类）
- **职责**: 管理完整的图形管线状态，提供状态的设置、获取、压栈和出栈操作，并通过 `StateGraph` 缓存 `GraphicsStateObject`（GSO）以提升性能。

#### 内部结构体

##### `Viewport`

定义渲染的视口区域。

| 成员 | 类型 | 说明 |
|------|------|------|
| `originX` | `float` | 左上角 X 坐标 |
| `originY` | `float` | 左上角 Y 坐标 |
| `width` | `float` | 视口宽度 |
| `height` | `float` | 视口高度 |
| `minDepth` | `float` | 最小深度值（0-1） |
| `maxDepth` | `float` | 最大深度值（0-1） |

##### `Scissor`

定义裁剪矩形区域。

| 成员 | 类型 | 说明 |
|------|------|------|
| `left` | `int32_t` | 左边界 |
| `top` | `int32_t` | 上边界 |
| `right` | `int32_t` | 右边界 |
| `bottom` | `int32_t` | 下边界 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<GraphicsState> create(ref<Device> pDevice)` | 静态工厂方法，创建新的 `GraphicsState` 实例。 |
| `ref<Fbo> getFbo() const` | 获取当前绑定的帧缓冲对象。 |
| `GraphicsState& setFbo(const ref<Fbo>& pFbo, bool setVp0Sc0 = true)` | 设置帧缓冲。当 `setVp0Sc0` 为 true 时，自动将视口 0 和裁剪矩形 0 设置为与 FBO 尺寸匹配。 |
| `void pushFbo(const ref<Fbo>& pFbo, bool setVp0Sc0 = true)` | 将当前 FBO 压栈并设置新的 FBO。适用于多通道渲染效果。 |
| `void popFbo(bool setVp0Sc0 = true)` | 从栈中恢复上一个 FBO。栈为空时会记录错误。 |
| `GraphicsState& setVao(const ref<Vao>& pVao)` | 设置顶点数组对象。传入 nullptr 则解绑当前 VAO。 |
| `ref<Vao> getVao() const` | 获取当前绑定的 VAO。 |
| `GraphicsState& setStencilRef(uint8_t refValue)` | 设置模板参考值。 |
| `uint8_t getStencilRef() const` | 获取当前模板参考值。 |
| `void setViewport(uint32_t index, const Viewport& vp, bool setScissors = true)` | 设置指定索引的视口。当 `setScissors` 为 true 时，同步设置对应裁剪矩形。 |
| `const Viewport& getViewport(uint32_t index) const` | 获取指定索引的视口。 |
| `const std::vector<Viewport>& getViewports() const` | 获取所有视口数组。 |
| `void pushViewport(uint32_t index, const Viewport& vp, bool setScissors = true)` | 将当前视口压栈并设置新视口。 |
| `void popViewport(uint32_t index, bool setScissors = true)` | 从栈中恢复指定索引的视口。 |
| `void setScissors(uint32_t index, const Scissor& sc)` | 设置指定索引的裁剪矩形。 |
| `const Scissor& getScissors(uint32_t index) const` | 获取指定索引的裁剪矩形。 |
| `void pushScissors(uint32_t index, const Scissor& sc)` | 将当前裁剪矩形压栈并设置新值。 |
| `void popScissors(uint32_t index)` | 从栈中恢复指定索引的裁剪矩形。 |
| `GraphicsState& setProgram(const ref<Program>& pProgram)` | 绑定着色器程序到管线。 |
| `ref<Program> getProgram() const` | 获取当前绑定的着色器程序。 |
| `GraphicsState& setBlendState(ref<BlendState> pBlendState)` | 设置混合状态。 |
| `ref<BlendState> getBlendState() const` | 获取当前混合状态。 |
| `GraphicsState& setRasterizerState(ref<RasterizerState> pRasterizerState)` | 设置光栅化状态。 |
| `ref<RasterizerState> getRasterizerState() const` | 获取当前光栅化状态。 |
| `GraphicsState& setDepthStencilState(ref<DepthStencilState> pDepthStencilState)` | 设置深度模板状态。 |
| `ref<DepthStencilState> getDepthStencilState() const` | 获取当前深度模板状态。 |
| `GraphicsState& setSampleMask(uint32_t sampleMask)` | 设置采样掩码。 |
| `uint32_t getSampleMask() const` | 获取当前采样掩码。 |
| `virtual ref<GraphicsStateObject> getGSO(const ProgramVars* pVars)` | 获取当前激活的图形管线状态对象（GSO）。虚函数，可被子类重写。 |
| `const GraphicsStateObjectDesc& getDesc() const` | 获取当前管线状态描述符。 |
| `void breakStrongReferenceToDevice()` | 断开对 Device 的强引用，用于打破循环引用。 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `BreakableReference<Device>` | GPU 设备引用（可断开的弱/强引用） |
| `mpVao` | `ref<Vao>` | 当前绑定的顶点数组对象 |
| `mpFbo` | `ref<Fbo>` | 当前绑定的帧缓冲对象 |
| `mpProgram` | `ref<Program>` | 当前绑定的着色器程序 |
| `mDesc` | `GraphicsStateObjectDesc` | 图形管线状态对象描述符 |
| `mStencilRef` | `uint8_t` | 模板参考值，默认为 0 |
| `mViewports` | `std::vector<Viewport>` | 视口数组 |
| `mScissors` | `std::vector<Scissor>` | 裁剪矩形数组 |
| `mFboStack` | `std::stack<ref<Fbo>>` | FBO 保存栈 |
| `mVpStack` | `std::vector<std::stack<Viewport>>` | 每个视口索引的视口保存栈 |
| `mScStack` | `std::vector<std::stack<Scissor>>` | 每个裁剪矩形索引的保存栈 |
| `mCachedData` | `CachedData` | 缓存数据 |
| `mpGsoGraph` | `unique_ptr<GraphicsStateGraph>` | 状态图实例，类型为 `StateGraph<ref<GraphicsStateObject>, void*>` |

## 依赖关系

### 本文件引用

| 头文件 | 说明 |
|--------|------|
| `StateGraph.h` | 状态图模板类，用于缓存管线状态对象 |
| `Core/Macros.h` | Falcor 宏定义 |
| `Core/Object.h` | 引用计数基类 |
| `Core/API/FBO.h` | 帧缓冲对象 |
| `Core/API/VAO.h` | 顶点数组对象 |
| `Core/API/DepthStencilState.h` | 深度模板状态 |
| `Core/API/RasterizerState.h` | 光栅化状态 |
| `Core/API/BlendState.h` | 混合状态 |
| `Core/API/GraphicsStateObject.h` | 图形管线状态对象及其描述符 |
| `Core/Program/Program.h` | 着色器程序类 |
| `Core/ObjectPython.h` | Python 绑定支持 |
| `Core/API/Device.h` | GPU 设备类 |
| `Core/Program/ProgramVars.h` | 着色器程序变量 |
| `Utils/Scripting/ScriptBindings.h` | 脚本绑定工具 |

### 被以下文件引用

| 文件 | 说明 |
|------|------|
| `Core/Pass/BaseGraphicsPass.h` | 图形渲染通道基类 |
| `Core/API/RenderContext.cpp` | 渲染上下文，执行实际的绘制调用 |
| `Scene/Lights/LightCollection.h` | 光源集合 |
| `Utils/UI/Gui.cpp` | GUI 渲染 |
| `Falcor.h` | Falcor 主头文件 |

## 实现细节

### 拓扑类型转换

文件内定义了辅助函数 `topology2Type()`，将 `Vao::Topology` 枚举转换为 `GraphicsStateObjectDesc::PrimitiveType`：

| Vao::Topology | PrimitiveType |
|---------------|---------------|
| `PointList` | `Point` |
| `LineList` / `LineStrip` | `Line` |
| `TriangleList` / `TriangleStrip` | `Triangle` |

### GSO 获取流程（`getGSO`）

1. **程序内核检测**: 获取当前着色器程序的活动内核，与缓存指针比较检测变化。若变化则在状态图中沿程序内核边前进。
2. **FBO 描述符检测**: 检查当前 FBO 描述符是否变化，若变化则沿 FBO 描述符边前进。
3. **节点查找**: 若当前节点无缓存的 GSO：
   - 更新描述符（程序内核、FBO 描述、顶点布局、图元类型）。
   - 在状态图中搜索匹配的 GSO。
   - 若未找到则创建新的 GSO，调用 `breakStrongReferenceToDevice()` 避免循环引用，并缓存到状态图。

### 状态栈机制

FBO、视口和裁剪矩形均支持压栈/出栈操作，便于在多通道渲染中临时切换状态后恢复。栈操作在出栈时会进行边界检查。

### 设备引用管理

`GraphicsState` 使用 `BreakableReference<Device>` 代替普通的 `ref<Device>`，并提供 `breakStrongReferenceToDevice()` 方法，用于打破与 Device 之间的循环引用，防止内存泄漏。

### Python 绑定

通过 `FALCOR_SCRIPT_BINDING` 宏将 `GraphicsState` 类注册到 pybind11。
