# InvalidPixelDetectionTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/DebugPasses/InvalidPixelDetectionTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/DebugPasses

## 功能概述

测试 InvalidPixelDetection 渲染通道，验证其能否正确检测帧缓冲中的 NaN 和 Inf 像素值。

## 测试用例

### GPU 测试

| 测试名称 | 说明 |
|----------|------|
| `InvalidPixelDetectionPass` | GPU 测试用例 |


## 依赖关系

### 本文件引用

- `Core/Plugin.h`
- `Testing/UnitTest.h`
- `RenderGraph/RenderGraph.h`


## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
