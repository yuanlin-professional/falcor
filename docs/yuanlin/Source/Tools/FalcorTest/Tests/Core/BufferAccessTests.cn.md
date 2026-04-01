# BufferAccessTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Core/BufferAccessTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Core

## 功能概述

测试不同内存类型（DeviceLocal、Upload、ReadBack）缓冲区的读写操作，包括 setBlob/getBlob 接口调用和 map/unmap 映射操作。

## 测试用例

### GPU 测试

| 测试名称 | 说明 |
|----------|------|
| `BufferDeviceLocalWrite` | GPU 测试用例 |
| `BufferDeviceLocalRead` | GPU 测试用例 |
| `BufferUploadWrite` | GPU 测试用例 |
| `BufferUploadMap` | GPU 测试用例 |
| `BufferReadbackRead` | GPU 测试用例 |
| `BufferReadbackMap` | GPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`


## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
