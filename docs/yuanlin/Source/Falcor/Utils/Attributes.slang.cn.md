# Attributes.slang 源码文档

> 路径: `Source/Falcor/Utils/Attributes.slang`
> 类型: Slang 着色器文件
> 模块: Utils

## 功能概述

定义用于标记资源的自定义着色器属性。当前仅包含 `rootAttribute`，用于标记应绑定到根描述符（root descriptor）的资源变量。

## 结构体与接口

### `rootAttribute`

- **类型**: 属性结构体
- **用途**: 用 `[__AttributeUsage(_AttributeTargets.Var)]` 修饰，表示该属性可应用于变量声明
- **职责**: 标记某个资源变量应使用根描述符而非描述符表进行绑定

## 函数

无。

## 依赖关系

### import

无外部依赖。

## 实现细节

- 这是一个空结构体，仅作为编译期属性标记使用
- Falcor 的着色器编译管线会识别此属性并相应调整资源绑定策略
