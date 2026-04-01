# ShaderString 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Slang/ShaderString.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Slang

## 功能概述

测试 Slang 着色器中的字符串处理功能，包括编译期字符串哈希和动态类型名称。

## 测试用例

### GPU 测试

| 测试名称 | 说明 |
|----------|------|
| `ShaderStringInline` | GPU 测试用例 |
| `ShaderStringModule` | GPU 测试用例 |
| `ShaderStringImport` | GPU 测试用例 |
| `ShaderStringImported` | GPU 测试用例 |
| `ShaderStringDynamicObject` | GPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `random`


## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
