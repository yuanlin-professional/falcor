# ComputeState 源码文档

> 路径: `Source/Falcor/Core/State/ComputeState.h` + `Source/Falcor/Core/State/ComputeState.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/State

## 功能概述

`ComputeState` 封装了执行一次计算调度（dispatch）所需的全部管线状态。它是一个可变对象，允许在渲染过程中动态修改。推荐的使用方式是为每个计算程序创建一个独立的 `ComputeState` 实例。该类内部通过 `StateGraph` 进行状态缓存，避免重复创建 `ComputeStateObject`（CSO），从而提升性能。

## 类与接口

### `ComputeState`

- **继承**: `Object`（Falcor 引用计数基类）
- **职责**: 管理计算管线状态，包括绑定的着色器程序，并通过状态图缓存机制高效地获取对应的 `ComputeStateObject`。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<ComputeState> create(ref<Device> pDevice)` | 静态工厂方法，创建一个新的 `ComputeState` 实例。需要传入 GPU 设备对象。 |
| `ComputeState& setProgram(ref<Program> pProgram)` | 将着色器程序绑定到计算管线。返回自身引用以支持链式调用。 |
| `ref<Program> getProgram() const` | 获取当前绑定的着色器程序。 |
| `ref<ComputeStateObject> getCSO(const ProgramVars* pVars)` | 获取当前激活的计算管线状态对象（CSO）。内部会根据程序变体和程序变量查找或创建对应的 CSO，并利用 `StateGraph` 缓存结果。 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | GPU 设备引用 |
| `mpProgram` | `ref<Program>` | 当前绑定的着色器程序 |
| `mDesc` | `ComputeStateObjectDesc` | 计算管线状态对象描述符，用于创建 CSO |
| `mCachedData` | `CachedData` | 缓存数据，存储上一次使用的 `ProgramKernels` 指针以检测变化 |
| `mpCsoGraph` | `unique_ptr<ComputeStateGraph>` | 状态图实例，类型为 `StateGraph<ref<ComputeStateObject>, void*>`，用于缓存和快速查找已创建的 CSO |

### 内部结构体 `CachedData`

| 成员 | 类型 | 说明 |
|------|------|------|
| `pProgramKernels` | `const ProgramKernels*` | 上一次使用的程序内核裸指针，用于检测程序是否发生变化 |

## 依赖关系

### 本文件引用

| 头文件 | 说明 |
|--------|------|
| `StateGraph.h` | 状态图模板类，用于缓存管线状态对象 |
| `Core/Macros.h` | Falcor 宏定义（如 `FALCOR_API`） |
| `Core/Object.h` | 引用计数基类 |
| `Core/API/ComputeStateObject.h` | 计算管线状态对象及其描述符 |
| `Core/Program/Program.h` | 着色器程序类 |
| `Core/ObjectPython.h` | Python 绑定支持 |
| `Core/Program/ProgramVars.h` | 着色器程序变量 |
| `Utils/Scripting/ScriptBindings.h` | 脚本绑定工具（pybind11） |

### 被以下文件引用

| 文件 | 说明 |
|------|------|
| `Core/Pass/ComputePass.h` | 计算渲染通道 |
| `Core/API/ComputeContext.cpp` | 计算上下文，执行实际的 dispatch 调用 |
| `Testing/UnitTest.h` | 单元测试框架 |
| `Utils/Algorithm/ParallelReduction.h` | 并行归约算法 |
| `Utils/Algorithm/PrefixSum.h` | 前缀和算法 |
| `Utils/Algorithm/BitonicSort.h` | 双调排序算法 |
| `Falcor.h` | Falcor 主头文件 |

## 实现细节

### CSO 获取流程（`getCSO`）

1. **程序内核检测**: 从当前绑定的着色器程序获取活动版本的内核（`ProgramKernels`）。通过与缓存的裸指针比较来判断程序是否发生变化。
2. **状态图导航**: 如果程序内核发生变化，调用 `mpCsoGraph->walk()` 沿状态图的边前进到对应节点。
3. **节点查找**: 若当前节点为空（无缓存的 CSO）：
   - 先更新描述符 `mDesc` 中的程序内核。
   - 通过 `scanForMatchingNode()` 在整个状态图中搜索匹配的 CSO。
   - 若找到则重用；否则调用 `mpDevice->createComputeStateObject(mDesc)` 创建新的 CSO 并缓存。

### Python 绑定

通过 `FALCOR_SCRIPT_BINDING` 宏将 `ComputeState` 类注册到 pybind11，使其可在 Python 脚本中使用。
