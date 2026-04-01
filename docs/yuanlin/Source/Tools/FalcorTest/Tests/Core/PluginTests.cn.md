# PluginTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Core/PluginTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Core

## 功能概述

测试 Falcor 插件管理器的加载和卸载功能，验证动态库插件的注册和实例化。

## 类与接口

### `PluginBaseA`

- **职责**: PluginBaseA 类的核心功能实现

### `PluginA1`

- **继承**: `PluginBaseA`
- **职责**: PluginA1 类的核心功能实现

### `PluginA2`

- **继承**: `PluginBaseA`
- **职责**: PluginA2 类的核心功能实现

### `PluginBaseB`

- **职责**: PluginBaseB 类的核心功能实现

### `PluginB1`

- **继承**: `PluginBaseB`
- **职责**: PluginB1 类的核心功能实现

### `PluginB2`

- **继承**: `PluginBaseB`
- **职责**: PluginB2 类的核心功能实现


## 结构体

- `PluginInfo`: 数据结构定义
- `PluginInfo`: 数据结构定义


## 测试用例

### CPU 测试

| 测试名称 | 说明 |
|----------|------|
| `Plugin` | CPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `Core/Plugin.h`


## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
