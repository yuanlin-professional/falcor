# ProgramVersion.h / ProgramVersion.cpp 源码文档

> 路径: `Source/Falcor/Core/Program/ProgramVersion.h`, `Source/Falcor/Core/Program/ProgramVersion.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/Program

## 功能概述

定义着色器程序的版本和内核层次结构。`ProgramVersion` 表示某组特定宏定义下编译得到的程序版本，`ProgramKernels` 表示经过类型特化后的可执行内核，`EntryPointKernel` 和 `EntryPointGroupKernels` 表示单个入口点及其分组的编译结果。这些类构成了从源码到 GPU 可执行代码的完整编译产物层次。

## 类与接口

### `EntryPointKernel`

- **继承**: `Object`
- **职责**: 表示单个着色器入口点及其关联的内核代码。实际代码生成被延迟到首次请求时执行

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<EntryPointKernel> create(Slang::ComPtr<slang::IComponentType>, ShaderType, const std::string&)` | 创建着色器内核对象 |
| `ShaderType getType() const` | 获取着色器类型 |
| `const std::string& getEntryPointName() const` | 获取入口点名称 |
| `BlobData getBlobData() const` | 获取编译后的二进制数据（惰性生成） |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mLinkedSlangEntryPoint` | `Slang::ComPtr<slang::IComponentType>` | Slang 链接后的入口点组件 |
| `mType` | `ShaderType` | 着色器类型 |
| `mEntryPointName` | `std::string` | 入口点名称 |
| `mpBlob` | `Slang::ComPtr<ISlangBlob>` | 编译后的二进制 Blob（惰性缓存） |

---

### `EntryPointGroupKernels`

- **继承**: `Object`
- **职责**: 入口点组的内核集合，按功能分为计算、光栅化、光线追踪单着色器和命中组四种类型

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<const EntryPointGroupKernels> create(Type, const std::vector<ref<EntryPointKernel>>&, const std::string&)` | 创建入口点组内核 |
| `Type getType() const` | 获取组类型 |
| `const EntryPointKernel* getKernel(ShaderType type) const` | 按着色器类型查找内核 |
| `const EntryPointKernel* getKernelByIndex(size_t index) const` | 按索引获取内核 |
| `const std::string& getExportName() const` | 获取导出名称 |

#### Type 枚举

| 值 | 说明 |
|----|------|
| `Compute` | 计算着色器组 |
| `Rasterization` | 光栅化着色器管线 |
| `RtSingleShader` | 光线追踪单着色器（raygen/miss/callable） |
| `RtHitGroup` | 光线追踪命中组 |

---

### `ProgramKernels`

- **继承**: `Object`
- **职责**: 整个程序经过类型特化后的内核集合，包含 GFX 着色器程序句柄

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<ProgramKernels> create(Device*, const ProgramVersion*, slang::IComponentType*, ...)` | 创建程序内核（含 GFX 程序编译） |
| `const EntryPointKernel* getKernel(ShaderType type) const` | 按类型查找内核 |
| `const std::string& getName() const` | 获取程序名称 |
| `const ref<const ProgramReflection>& getReflector() const` | 获取反射对象 |
| `const UniqueEntryPointGroups& getUniqueEntryPointGroups() const` | 获取所有入口点组 |
| `gfx::IShaderProgram* getGfxProgram() const` | 获取 GFX 着色器程序句柄 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mGfxProgram` | `Slang::ComPtr<gfx::IShaderProgram>` | GFX 着色器程序 |
| `mUniqueEntryPointGroups` | `UniqueEntryPointGroups` | 入口点组内核列表 |
| `mpReflector` | `ref<const ProgramReflection>` | 特化后的反射对象 |
| `mpVersion` | `ProgramVersion const*` | 所属程序版本 |

---

### `ProgramVersion`

- **继承**: `Object`
- **职责**: 表示程序在特定宏定义下的编译版本，持有 Slang 全局作用域和入口点，按需创建特化内核

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `Program* getProgram() const` | 获取所属程序 |
| `const DefineList& getDefines() const` | 获取此版本使用的宏定义 |
| `const std::string& getName() const` | 获取版本名称 |
| `const ref<const ProgramReflection>& getReflector() const` | 获取反射对象 |
| `ref<const ProgramKernels> getKernels(Device*, ProgramVars const*) const` | 根据变量绑定获取/创建特化内核 |
| `slang::ISession* getSlangSession() const` | 获取 Slang 会话 |
| `slang::IComponentType* getSlangGlobalScope() const` | 获取 Slang 全局作用域 |
| `slang::IComponentType* getSlangEntryPoint(uint32_t index) const` | 获取指定入口点 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpProgram` | `Program*` | 所属程序（可变，析构时置空） |
| `mDefines` | `DefineList` | 此版本的宏定义 |
| `mpReflector` | `ref<const ProgramReflection>` | 反射对象 |
| `mpSlangGlobalScope` | `Slang::ComPtr<slang::IComponentType>` | Slang 全局作用域组件 |
| `mpSlangEntryPoints` | `std::vector<Slang::ComPtr<slang::IComponentType>>` | Slang 入口点列表 |
| `mpKernels` | `std::unordered_map<std::string, ref<const ProgramKernels>>` | 特化内核缓存 |

## 依赖关系

### 本文件引用

- `ProgramReflection.h` — 反射类型定义
- `DefineList.h` — 宏定义列表
- `Core/Object.h` — 基类
- `Core/API/Types.h`, `Core/API/Handles.h` — API 类型和句柄
- `<slang.h>` — Slang 编译器 API
- `Program.h`, `ProgramManager.h`, `ProgramVars.h` — 编译流程（.cpp）

### 被以下文件引用

- `Program.h` — 通过 `#include "ProgramVersion.h"` 引用
- `ProgramManager.cpp` — 创建版本和内核
- `ProgramVars.cpp` — 着色器表准备

## 实现细节

- **两阶段编译**: `ProgramVersion` 只执行 Slang 前端编译（无代码生成），`ProgramKernels` 在需要时执行后端代码生成和特化
- **特化键计算**: `getKernels` 通过收集 `ProgramVars` 中的特化参数（接口类型绑定）构建字符串键进行内核缓存查找
- **延迟代码生成**: `EntryPointKernel::getBlobData()` 惰性调用 `getEntryPointCode()`，避免不必要的着色器编译
- **光线追踪入口点去重**: `ProgramKernels::create` 中对光线追踪程序的入口点按导出名称去重，避免着色器表中的冗余条目
- **GFX 程序创建**: 使用 `SeparateEntryPointCompilation` 链接风格，将全局作用域和各入口点组件分别传递给 GFX API
- **错误重试**: `getKernels` 和 `link` 均支持在编译失败时弹出对话框让用户选择重试（用于开发阶段调试）
