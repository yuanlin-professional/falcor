# ComputePass 源码文档

> 路径: `Source/Falcor/Core/Pass/ComputePass.h` + `ComputePass.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/Pass

## 功能概述

`ComputePass` 封装了计算着色器渲染通道的完整执行流程，包括计算管线状态管理、着色器程序变量绑定、线程分发（直接分发和间接分发）。该类还提供了 Python 脚本绑定，支持通过 Python 脚本创建和执行计算通道。

## 类与接口

### `ComputePass`

- **继承**: `Object`（Falcor 基础引用计数对象）
- **职责**: 管理计算着色器的管线状态和程序变量，提供直接分发和间接分发两种执行方式

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<ComputePass> create(ref<Device>, const std::filesystem::path&, const std::string& csEntry = "main", const DefineList& = DefineList(), bool createVars = true)` | 从着色器文件路径创建计算通道 |
| `static ref<ComputePass> create(ref<Device>, const ProgramDesc&, const DefineList& = DefineList(), bool createVars = true)` | 从程序描述创建计算通道 |
| `virtual void execute(ComputeContext* pContext, uint32_t nThreadX, uint32_t nThreadY, uint32_t nThreadZ = 1)` | 按线程数（非线程组数）分发计算任务，内部自动计算线程组数量 |
| `virtual void execute(ComputeContext* pContext, const uint3& nThreads)` | 以 `uint3` 形式指定线程数的分发重载 |
| `virtual void executeIndirect(ComputeContext* context, const Buffer* pArgBuffer, uint64_t argBufferOffset = 0)` | 间接分发，从参数缓冲区读取分发参数 |
| `bool hasVars() const` | 检查是否已创建程序变量 |
| `const ref<ProgramVars>& getVars() const` | 获取程序变量 |
| `ShaderVar getRootVar() const` | 获取着色器变量的根节点 |
| `void addDefine(const std::string& name, const std::string& value = "", bool updateVars = false)` | 添加着色器宏定义 |
| `void removeDefine(const std::string& name, bool updateVars = false)` | 移除着色器宏定义 |
| `ref<Program> getProgram() const` | 获取当前着色器程序 |
| `void setVars(const ref<ProgramVars>& pVars)` | 设置或替换程序变量，传入 `nullptr` 时自动创建 |
| `uint3 getThreadGroupSize() const` | 从程序反射中获取线程组大小 |
| `const ref<Device>& getDevice() const` | 获取 GPU 设备引用 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | GPU 设备引用 |
| `mpVars` | `ref<ProgramVars>` | 着色器程序变量 |
| `mpState` | `ref<ComputeState>` | 计算管线状态 |

## 依赖关系

### 本文件引用

- `Core/Macros.h` — Falcor 宏定义
- `Core/Object.h` — 基础引用计数对象
- `Core/State/ComputeState.h` — 计算管线状态
- `Core/Program/Program.h` — 着色器程序
- `Core/Program/ProgramVars.h` — 程序变量
- `Core/Program/ShaderVar.h` — 着色器变量访问
- `Core/API/ComputeContext.h` — 计算上下文（实现文件）
- `Core/API/PythonHelpers.h` — Python 辅助函数（实现文件）
- `Utils/Math/Common.h` — 数学工具函数（实现文件）
- `Utils/Scripting/ScriptBindings.h` — 脚本绑定工具（实现文件）

### 被以下文件引用

- 各类需要计算着色器分发的渲染通道和后处理效果

## 实现细节

- `execute()` 方法接收的参数为总线程数而非线程组数。内部通过 `div_round_up(nThreads, threadGroupSize)` 计算实际需要分发的线程组数量，简化了调用方的使用。
- `executeIndirect()` 支持从 GPU 缓冲区读取分发参数，适用于 GPU 驱动的工作负载。
- 构造时可通过 `createVars` 参数控制是否自动创建程序变量，延迟创建可用于需要手动设置变量的场景。
- 提供了完整的 Python 脚本绑定（`FALCOR_SCRIPT_BINDING`），支持通过 `ProgramDesc` 或关键字参数创建实例，并暴露了 `program`、`root_var`、`globals` 属性和 `execute` 方法。
