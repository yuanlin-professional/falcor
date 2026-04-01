# ResourceAliasing 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Core/ResourceAliasing.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Core

## 功能概述

测试 GPU 资源别名（Resource Aliasing）功能，验证两个资源共享相同物理内存时的屏障和同步操作。

## 测试用例

### GPU 测试

| 测试名称 | 说明 |
|----------|------|
| `BufferAliasing_Read` | GPU 测试用例 |
| `BufferAliasing_ReadWrite` | GPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`


### 关联着色器

- [`ResourceAliasing.cs.slang`](ResourceAliasing.cs.slang.md)

## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
