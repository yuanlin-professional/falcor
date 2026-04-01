# MemoryMappedFileTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Platform/MemoryMappedFileTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Platform

## 功能概述

测试内存映射文件的创建、读写和同步操作，验证文件内存映射机制的正确性。

## 测试用例

### CPU 测试

| 测试名称 | 说明 |
|----------|------|
| `MemoryMappedFile_Closed` | CPU 测试用例 |
| `MemoryMappedFile_NonExisting` | CPU 测试用例 |
| `MemoryMappedFile_Read` | CPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `Core/Platform/OS.h`
- `Core/Platform/MemoryMappedFile.h`
- `vector`
- `fstream`
- `random`


## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
