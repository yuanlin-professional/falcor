# TextureAnalyzerTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Utils/TextureAnalyzerTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Utils

## 功能概述

测试纹理分析工具，包括像素统计（最大值、最小值、均值）和异常值检测。

## 测试用例

### GPU 测试

| 测试名称 | 说明 |
|----------|------|
| `TextureAnalyzer` | GPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `Utils/Image/TextureAnalyzer.h`


## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
