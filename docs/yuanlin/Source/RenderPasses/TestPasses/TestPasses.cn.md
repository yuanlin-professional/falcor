# TestPasses 源码文档

> 路径: `Source/RenderPasses/TestPasses/TestPasses.cpp`
> 类型: C++ 实现（插件注册入口）
> 模块: RenderPasses/TestPasses

## 功能概述

TestPasses.cpp 是测试渲染通道模块的插件注册入口文件。它将两个测试渲染通道注册到 Falcor 的插件系统中，并注册对应的 Python 脚本绑定。

## 注册的渲染通道

| 渲染通道 | 说明 |
|----------|------|
| `TestRtProgram` | 光线追踪程序测试通道，测试 RtProgram 的多种配置 |
| `TestPyTorchPass` | PyTorch 张量互操作测试通道，测试 CUDA/DX 共享缓冲区 |

## 依赖关系

### 本文件引用

- `TestRtProgram.h`
- `TestPyTorchPass.h`
