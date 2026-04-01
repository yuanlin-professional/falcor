# ShaderTable 源码文档

> 路径: `Source/Falcor/Core/API/ShaderTable.h`
> 类型: C++ 头文件
> 模块: Core/API

## 功能概述

ShaderTable.h 定义了光线追踪着色器表的布局说明和一个 RAII 包装类 `ShaderTablePtr`。着色器表是光线追踪管线中连接射线类型与着色器程序的核心数据结构。

## 着色器表布局

着色器表按以下顺序排列记录：
1. **RayGen 记录**（1 个）
2. **Miss 记录**（M 个，对应 M 种 miss 着色器）
3. **Hit Group 记录**（N x K 个，N 为几何体数量，K 为射线类型数量）

每条记录默认包含 32 字节的程序标识符。

## 类与接口

### `ShaderTablePtr`
- **职责**: 对 `gfx::IShaderTable` 的 RAII 包装，析构时通过 Device 延迟释放

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `gfx::IShaderTable& operator*()` | 解引用 |
| `gfx::IShaderTable* operator->()` | 指针访问 |
| `gfx::IShaderTable* get()` | 获取裸指针 |
| `gfx::IShaderTable** writeRef()` | 获取写引用（用于创建） |

## 依赖关系
### 本文件引用
- `Device.h`, `slang-gfx.h`

### 被以下文件引用
- 光线追踪程序变量相关模块
