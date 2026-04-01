# SlangUtils.slang 源码文档

> 路径: `Source/Falcor/Utils/SlangUtils.slang`
> 类型: Slang 着色器文件
> 模块: Utils

## 功能概述

提供 Slang 着色器端的通用工具。当前定义了 `ArrayMax` 模板结构体，用于确保着色器中数组大小不为零。

## 结构体与接口

### `ArrayMax<let M : int, let N : int>`

- **类型**: 编译期常量模板结构体
- **职责**: 计算两个整数的最大值，用于避免零大小数组

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `value` | `static const int` | `M` 和 `N` 中的较大值 |

### 使用模式

```slang
float name[ArrayMax<SIZE, 1>.value]  // 数组大小至少为 1
```

## 函数

无独立函数定义。

## 依赖关系

### import

无外部依赖。

## 实现细节

- 使用 Slang 的泛型参数（`let M : int`）实现编译期常量计算
- 通过三元运算符实现取最大值逻辑
