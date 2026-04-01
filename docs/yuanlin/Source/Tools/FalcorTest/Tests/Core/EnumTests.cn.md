# EnumTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Core/EnumTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Core

## 功能概述

测试 Falcor 枚举系统的功能，包括枚举信息注册、枚举值与字符串之间的转换等。

## 类与接口

### `TestEnum`

- **职责**: TestEnum 类的核心功能实现

### `TestFlags`

- **职责**: TestFlags 类的核心功能实现

### `TestEnum`

- **职责**: TestEnum 类的核心功能实现


## 结构体

- `TestStruct`: 数据结构定义


## 枚举类型

- `TestEnum`
- `TestFlags`
- `TestEnum`
- `nested`


## 测试用例

### CPU 测试

| 测试名称 | 说明 |
|----------|------|
| `EnumInfo` | CPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `Core/Enum.h`


## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
