# LockFileTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Platform/LockFileTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Platform

## 功能概述

测试跨进程文件锁（LockFile）的获取和释放功能，验证互斥文件访问的正确性。

## 结构体

- `LockTask`: 数据结构定义


## 测试用例

### CPU 测试

| 测试名称 | 说明 |
|----------|------|
| `LockFile_Closed` | CPU 测试用例 |
| `LockFile_OpenClose` | CPU 测试用例 |
| `LockFile_ExclusiveLock` | CPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `Core/Platform/OS.h`
- `Core/Platform/LockFile.h`
- `atomic`
- `future`
- `thread`
- `vector`


## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
