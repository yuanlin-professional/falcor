# FalcorTest 源码文档

> 路径: `Source/Tools/FalcorTest/FalcorTest.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest

## 功能概述

这是 Falcor 单元测试框架的主入口程序。它解析命令行参数，配置测试运行选项（如设备类型、GPU 选择、测试过滤器等），然后调用 `unittest::runTests()` 执行所有匹配的测试用例。支持 D3D12 和 Vulkan 两种图形 API，提供测试套件/用例列表、标签过滤和 XML 报告输出等功能。

## 依赖关系

### 本文件引用

- `Core/Error.h`
- `Utils/StringUtils.h`
- `Testing/UnitTest.h`
- `args.hxx`
- `cstdio`
- `string`
- `vector`


## 实现细节

程序通过 `args::ArgumentParser` 解析命令行参数，支持以下主要选项：

- `-d/--device-type`: 选择图形 API（d3d12 或 vulkan）
- `-s/--test-suite`: 按正则表达式过滤测试套件
- `-f/--test-case`: 按正则表达式过滤测试用例
- `-t/--tags`: 按标签过滤测试
- `-x/--xml-report`: 输出 XML 格式测试报告
- `-p/--parallel`: 实验性并行测试线程数
- `--gpu`: 选择指定 GPU
- `--enable-debug-layer`: 启用图形 API 调试层
- `--enable-aftermath`: 启用 NVIDIA Aftermath 崩溃转储

测试框架会在运行前禁用 `BreakOnThrow` 错误诊断标志，以确保测试在附加调试器时不会因异常而中断。
