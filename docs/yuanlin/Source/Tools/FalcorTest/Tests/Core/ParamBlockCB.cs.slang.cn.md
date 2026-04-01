# ParamBlockCB.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Core/ParamBlockCB.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Core

## 功能概述

测试 ParameterBlock 中嵌套常量缓冲区的绑定和数据传递，验证复杂的着色器参数布局。

## 结构体与接口

### `S`

结构体定义。


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `result` | `RWStructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `main` | [1, 1, 1] | 计算着色器入口点 |


### 关联 C++ 测试

- [`ParamBlockCB.cpp`](ParamBlockCB.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
