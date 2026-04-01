# TestPyTorchPass 源码文档

> 路径: `Source/RenderPasses/TestPasses/TestPyTorchPass.h` + `TestPyTorchPass.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/TestPasses

## 功能概述

TestPyTorchPass 是一个用于测试 PyTorch 张量与 Falcor GPU 缓冲区互操作的渲染通道。它通过 CUDA/DX 共享缓冲区实现了数据在 Falcor 计算着色器和 PyTorch 张量之间的异步传递，提供 `generateData`（生成数据并返回 PyTorch 张量）和 `verifyData`（验证 PyTorch 张量内容）两个功能。

## 类与接口

### `TestPyTorchPass`

- **继承**: `RenderPass`
- **职责**: 测试 Falcor 与 PyTorch 之间的张量数据互操作

#### 类型别名

| 别名 | 定义 | 说明 |
|------|------|------|
| `PyTorchTensor` | `pybind11::ndarray<pybind11::pytorch, float>` | PyTorch 浮点张量类型 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `TestPyTorchPass(ref<Device>, const Properties&)` | 构造函数，创建读写计算通道和计数器缓冲区，初始化 CUDA |
| `~TestPyTorchPass()` | 析构函数，释放共享 CUDA 缓冲区 |
| `PyTorchTensor generateData(uint3 dim, uint32_t offset)` | 生成 3D 数据（值 = offset + 线性索引）并返回 CUDA PyTorch 张量 |
| `bool verifyData(uint3 dim, uint32_t offset, PyTorchTensor)` | 验证 PyTorch 张量内容是否符合预期 |
| `static void registerScriptBindings(pybind11::module&)` | 注册 generateData 和 verifyData 为 Python 方法 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpBuffer` | `ref<Buffer>` | GPU 数据缓冲区 |
| `mpCounterBuffer` | `ref<Buffer>` | 验证计数器缓冲区 |
| `mpCounterStagingBuffer` | `ref<Buffer>` | 计数器读回暂存缓冲区 |
| `mSharedWriteBuffer` | `InteropBuffer` | CUDA/Falcor 共享写缓冲区（Falcor -> PyTorch） |
| `mSharedReadBuffer` | `InteropBuffer` | CUDA/Falcor 共享读缓冲区（PyTorch -> Falcor） |
| `mpWritePass` / `mpReadPass` | `ref<ComputePass>` | 数据生成/验证计算通道 |

## 依赖关系

### 本文件引用

- `Falcor.h`
- `RenderGraph/RenderPass.h`
- `Utils/Scripting/ndarray.h`
- `Utils/CudaUtils.h`（条件编译，FALCOR_HAS_CUDA）
- `TestPyTorchPass.cs.slang`（着色器）

### 被以下文件引用

- `TestPasses.cpp`（插件注册）

## 实现细节

1. **CUDA 互操作**: 使用 `createInteropBuffer` 创建 CUDA/DX 共享缓冲区，Falcor 写入后通过 `copyResource` 传递到共享缓冲区，PyTorch 可直接在 CUDA 端访问。
2. **异步数据流**: 生成数据时，Falcor 保留缓冲区所有权，PyTorch 可在下次调用前自由访问。调用方负责同步。
3. **验证流程**: 从 PyTorch CUDA 张量复制到共享缓冲区 -> 等待 CUDA 完成 -> 运行 GPU 计算通道统计匹配元素数 -> 读回计数器结果。
4. **张量布局验证**: 在 `verifyData` 中检查张量的设备类型、数据类型、维度和步幅是否符合预期。
