# OSTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Platform/OSTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Platform

## 功能概述

测试 Falcor 操作系统抽象层的功能，包括文件系统操作、进程管理和系统信息查询。

## 测试用例

### CPU 测试

| 测试名称 | 说明 |
|----------|------|
| `Junction` | CPU 测试用例 |
| `HasExtension` | CPU 测试用例 |
| `GetExtensionFromPath` | CPU 测试用例 |
| `OS_HomeDirectory` | CPU 测试用例 |
| `GetEnvironmentVariable` | CPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `Core/Platform/OS.h`


## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
