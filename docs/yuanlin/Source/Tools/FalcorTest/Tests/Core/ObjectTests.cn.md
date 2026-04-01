# ObjectTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Core/ObjectTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Core

## 功能概述

测试 Falcor 对象引用计数系统（ref<T>）以及弱引用（BreakableReference）机制的正确性。

## 类与接口

### `DummyObject`

- **继承**: `Object`
- **职责**: DummyObject 类的核心功能实现

### `DummyDevice`

- **继承**: `Object`
- **职责**: DummyDevice 类的核心功能实现

### `DummyBuffer`

- **继承**: `Object`
- **职责**: DummyBuffer 类的核心功能实现


## 测试用例

### CPU 测试

| 测试名称 | 说明 |
|----------|------|
| `Object_ref` | CPU 测试用例 |
| `Object_BreakableReference` | CPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `Core/Object.h`


## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
