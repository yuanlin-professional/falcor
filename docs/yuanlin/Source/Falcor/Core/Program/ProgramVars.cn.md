# ProgramVars.h / ProgramVars.cpp 源码文档

> 路径: `Source/Falcor/Core/Program/ProgramVars.h`, `Source/Falcor/Core/Program/ProgramVars.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/Program

## 功能概述

提供着色器程序变量的管理接口。`ProgramVars` 是对着色器资源绑定的高层抽象，封装了常量缓冲区（CB）、纹理、采样器、根签名、描述符表等底层概念。`RtProgramVars` 是其光线追踪特化版本，额外管理着色器表（Shader Table）的构建。

## 类与接口

### `ProgramVars`

- **继承**: `ParameterBlock`
- **职责**: 管理着色器程序的反射信息和变量赋值，作为所有着色器资源绑定的入口

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<ProgramVars> create(ref<Device>, const ref<const ProgramReflection>&)` | 从反射对象创建 |
| `static ref<ProgramVars> create(ref<Device>, const Program*)` | 从程序对象创建（使用活跃版本） |
| `const ref<const ProgramReflection>& getReflection() const` | 获取程序反射接口 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpReflector` | `ref<const ProgramReflection>` | 程序反射对象 |

---

### `RtProgramVars`

- **继承**: `ProgramVars`
- **职责**: 管理光线追踪程序的变量和着色器表，处理 raygen、miss 和 hit group 的绑定

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<RtProgramVars> create(ref<Device>, const ref<Program>&, const ref<RtBindingTable>&)` | 从光线追踪程序和绑定表创建 |
| `bool prepareShaderTable(RenderContext*, RtStateObject*)` | 准备 GPU 着色器表 |
| `ShaderTablePtr getShaderTable() const` | 获取着色器表 |
| `uint32_t getMissVarsCount() const` | 获取 miss 着色器变量数量 |
| `uint32_t getTotalHitVarsCount() const` | 获取 hit group 变量总数 |
| `uint32_t getRayTypeCount() const` | 获取光线类型数量 |
| `uint32_t getGeometryCount() const` | 获取几何体数量 |
| `const std::vector<int32_t>& getUniqueEntryPointGroupIndices() const` | 获取唯一入口点组索引 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mRayTypeCount` | `uint32_t` | 光线类型数量 |
| `mGeometryCount` | `uint32_t` | 几何体数量 |
| `mUniqueEntryPointGroupIndices` | `std::vector<int32_t>` | 使用的唯一入口点组索引 |
| `mpShaderTable` | `ShaderTablePtr` | GPU 着色器表 |
| `mpCurrentRtStateObject` | `RtStateObject*` | 当前着色器表对应的状态对象 |
| `mRayGenVars` | `VarsVector` | 光线生成着色器变量 |
| `mMissVars` | `VarsVector` | 未命中着色器变量 |
| `mHitVars` | `VarsVector` | 命中组着色器变量 |

## 依赖关系

### 本文件引用

- `RtBindingTable.h` — 光线追踪绑定表
- `Core/API/ParameterBlock.h` — 基类
- `Core/API/ShaderTable.h` — 着色器表
- `Program.h` — 程序对象（.cpp）
- `Core/API/RenderContext.h` — 渲染上下文（.cpp）
- `Core/API/GFXAPI.h` — GFX API 接口（.cpp）

### 被以下文件引用

- `ProgramManager.cpp` — 在创建程序内核时使用
- `ProgramVersion.cpp` — 收集特化参数
- `Program.cpp` — getRtso 中使用

## 实现细节

- **ProgramVars 创建**: 直接委托给 `ParameterBlock` 基类构造，以反射对象为基础分配资源槽位
- **RtProgramVars 初始化** (`init`): 根据 `RtBindingTable` 的配置分配 raygen（固定 1 个）、miss（按 miss 数量）和 hit group（按 rayType * geometryCount）的变量空间。同时收集所有唯一的入口点组索引
- **着色器表准备** (`prepareShaderTable`): 当状态对象变化时重建着色器表。从入口点组内核中提取着色器标识符，通过 GFX API 的 `createShaderTable` 创建 GPU 端着色器表
- **Hit Group 索引映射**: hit group 变量按 `mRayTypeCount * geometryID + rayType` 的线性索引存储，与 DXR 的着色器表布局一致
