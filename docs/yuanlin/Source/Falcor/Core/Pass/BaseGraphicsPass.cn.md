# BaseGraphicsPass 源码文档

> 路径: `Source/Falcor/Core/Pass/BaseGraphicsPass.h` + `BaseGraphicsPass.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/Pass

## 功能概述

`BaseGraphicsPass` 是所有图形渲染通道的抽象基类，封装了图形管线状态（`GraphicsState`）和程序变量（`ProgramVars`）的管理逻辑。它提供了着色器宏定义的增删操作、程序变量的设置与获取等通用功能，供子类 `FullScreenPass` 和 `RasterPass` 继承使用。

## 类与接口

### `BaseGraphicsPass`

- **继承**: `Object`（Falcor 基础引用计数对象）
- **职责**: 作为图形渲染通道的基类，管理图形管线状态和着色器程序变量的生命周期

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void addDefine(const std::string& name, const std::string& value = "", bool updateVars = false)` | 向着色器程序添加宏定义。若 `updateVars` 为 `true`，则重新创建程序变量 |
| `void removeDefine(const std::string& name, bool updateVars = false)` | 从着色器程序移除宏定义。若 `updateVars` 为 `true`，则重新创建程序变量 |
| `ref<Program> getProgram() const` | 获取当前关联的着色器程序 |
| `const ref<GraphicsState>& getState() const` | 获取图形管线状态对象 |
| `const ref<ProgramVars>& getVars() const` | 获取程序变量对象 |
| `ShaderVar getRootVar() const` | 获取着色器变量的根节点，用于绑定资源 |
| `void setVars(const ref<ProgramVars>& pVars)` | 设置程序变量对象。传入 `nullptr` 时自动创建新的变量对象。可用于在不同渲染通道间共享变量 |
| `void breakStrongReferenceToDevice()` | 断开对 GPU 设备的强引用，防止循环引用 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `BreakableReference<Device>` | 可断开的 GPU 设备引用 |
| `mpVars` | `ref<ProgramVars>` | 着色器程序变量 |
| `mpState` | `ref<GraphicsState>` | 图形管线状态 |

#### 构造函数（受保护）

| 方法签名 | 说明 |
|----------|------|
| `BaseGraphicsPass(ref<Device> pDevice, const ProgramDesc& progDesc, const DefineList& programDefines)` | 根据程序描述和宏定义列表创建着色器程序、图形管线状态和程序变量 |

## 依赖关系

### 本文件引用

- `Core/Macros.h` — Falcor 宏定义
- `Core/Object.h` — 基础引用计数对象
- `Core/State/GraphicsState.h` — 图形管线状态
- `Core/Program/Program.h` — 着色器程序
- `Core/Program/ProgramVars.h` — 程序变量
- `Core/Program/ShaderVar.h` — 着色器变量访问

### 被以下文件引用

- `FullScreenPass.h` — 全屏渲染通道继承此类
- `RasterPass.h` — 光栅化渲染通道继承此类

## 实现细节

- 构造函数中依次创建 `Program`、`GraphicsState`、`ProgramVars`，并将程序设置到管线状态中。同时对程序和管线状态调用 `breakStrongReferenceToDevice()` 以避免循环引用。
- `addDefine` / `removeDefine` 在修改着色器宏定义后，可选地重新创建 `ProgramVars` 以匹配新的程序反射信息。
- `setVars` 支持传入 `nullptr`，此时会基于当前程序自动创建新的变量对象，方便重置或共享变量。
