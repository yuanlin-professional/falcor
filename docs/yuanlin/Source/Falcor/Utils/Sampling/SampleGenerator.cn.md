# SampleGenerator 源码文档

> 路径: `Source/Falcor/Utils/Sampling/SampleGenerator.h` + `Source/Falcor/Utils/Sampling/SampleGenerator.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Sampling

## 功能概述

提供 GPU 采样生成器的 **主机端管理框架**。该类负责配置着色器程序（通过宏定义选择采样器类型）、上传必要的查找表，并提供工厂模式用于创建不同类型的采样生成器。GPU 端应导入 `SampleGenerator.slang` 使用对应的采样器。

系统内置两种基本采样器类型：
- **Tiny Uniform (32-bit)** — 轻量级 32 位均匀随机生成器
- **Uniform (128-bit)** — 标准 128 位均匀随机生成器

支持通过 `registerType` 机制扩展自定义采样器类型。

## 类与接口

### `SampleGenerator`

- **继承**: `Object`（Falcor 引用计数基类）
- **职责**: GPU 采样生成器的主机端基类，管理采样器类型注册、创建、着色器宏定义和数据绑定

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<SampleGenerator> create(ref<Device> pDevice, uint32_t type)` | 工厂方法，根据类型 ID 创建对应的采样生成器实例 |
| `virtual DefineList getDefines() const` | 返回着色器宏定义列表，设置 `SAMPLE_GENERATOR_TYPE` 宏以选择 GPU 端采样器类型 |
| `virtual void bindShaderData(const ShaderVar& var) const` | 将采样器数据绑定到着色器变量（默认空实现） |
| `virtual void renderUI(Gui::Widgets& widget)` | 渲染采样器的 UI 控件（默认空实现） |
| `virtual bool beginFrame(RenderContext* pRenderContext, const uint2& frameDim)` | 每帧开始时调用，用于需要逐帧设置的采样器。返回 true 表示内部状态已变更 |
| `virtual void endFrame(RenderContext* pRenderContext, const ref<Texture>& pRenderOutput)` | 每帧结束时调用 |
| `static const Gui::DropdownList& getGuiDropdownList()` | 返回所有可用采样生成器类型的 GUI 下拉列表 |
| `static void registerType(uint32_t type, const std::string& name, std::function<ref<SampleGenerator>(ref<Device>)> createFunc)` | 注册新的采样生成器类型 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | GPU 设备引用 |
| `mType` | `const uint32_t` | 采样生成器类型 ID（参见 SampleGeneratorType.slangh） |

## 依赖关系

### 本文件引用

- `SampleGeneratorType.slangh` — 采样器类型常量定义
- `Core/Macros.h` — Falcor 宏定义
- `Core/Object.h` — 引用计数基类
- `Core/Program/DefineList.h` — 着色器宏定义列表
- `Core/Program/ShaderVar.h` — 着色器变量绑定
- `Utils/UI/Gui.h` — GUI 支持

### 被以下文件引用

- `SampleGenerator.slang` — GPU 端采样器选择逻辑
- 各种渲染通道（RenderPass）中需要使用 GPU 随机采样的模块

## 实现细节

- 使用 **工厂模式 + 注册机制**：通过静态 `std::map` (`sFactory`) 存储类型到创建函数的映射
- 使用静态初始化对象 `RegisterSampleGenerators` 在程序启动时自动注册内置采样器类型
- `getDefines()` 方法设置 `SAMPLE_GENERATOR_TYPE` 宏，GPU 端的 `SampleGenerator.slang` 根据此宏选择对应的采样器实现
- 内置注册了两种类型：`SAMPLE_GENERATOR_TINY_UNIFORM`（类型 0）和 `SAMPLE_GENERATOR_UNIFORM`（类型 1）
- 虚函数设计允许派生类实现更复杂的采样策略（如需要额外查找表或逐帧状态更新）
