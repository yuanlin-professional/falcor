# PropertiesTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Utils/PropertiesTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Utils

## 功能概述

测试 Falcor 的动态属性（Properties）系统，包括类型安全的键值存取。

## 类与接口

### `TestEnum`

- **职责**: TestEnum 类的核心功能实现


## 结构体

- `TestStruct`: 数据结构定义
- `NestedStruct`: 数据结构定义
- `TestStruct`: 数据结构定义


## 枚举类型

- `TestEnum`


## 测试用例

### CPU 测试

| 测试名称 | 说明 |
|----------|------|
| `PropertiesBasicValues` | CPU 测试用例 |
| `PropertiesFromJson` | CPU 测试用例 |
| `PropertiesToJson` | CPU 测试用例 |
| `PropertiesFromPython` | CPU 测试用例 |
| `PropertiesToPython` | CPU 测试用例 |
| `PropertiesAccessors` | CPU 测试用例 |
| `PropertiesIterators` | CPU 测试用例 |
| `PropertiesSerialization` | CPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `Core/Enum.h`
- `Utils/Properties.h`
- `Utils/Logger.h`
- `Utils/StringFormatters.h`
- `nlohmann/json.hpp`
- `pybind11/pybind11.h`
- `pybind11/stl.h`


## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
