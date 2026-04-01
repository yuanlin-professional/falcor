# Program.h / Program.cpp 源码文档

> 路径: `Source/Falcor/Core/Program/Program.h`, `Source/Falcor/Core/Program/Program.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/Program

## 功能概述

定义着色器程序的高层抽象，包括程序描述（`ProgramDesc`）、类型一致性（`TypeConformance`）和程序主类（`Program`）。`Program` 类管理同一程序在不同宏定义和类型一致性配置下的多个版本，通过懒编译（lazy compilation）策略实现按需编译和链接。支持传统光栅化着色器、计算着色器及光线追踪着色器。

## 类与接口

### `TypeConformance`

- **继承**: 无
- **职责**: 表示着色器接口的类型实现关系（如某类型实现了某接口）

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `typeName` | `std::string` | 实现类型名称 |
| `interfaceName` | `std::string` | 接口类型名称 |

---

### `TypeConformanceList`

- **继承**: `std::map<TypeConformance, uint32_t>`
- **职责**: 管理类型一致性列表，将类型-接口对映射到 ID

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `TypeConformanceList& add(const std::string& typeName, const std::string& interfaceName, uint32_t id = -1)` | 添加类型一致性条目 |
| `TypeConformanceList& remove(const std::string& typeName, const std::string& interfaceName)` | 移除类型一致性条目 |
| `TypeConformanceList& add(const TypeConformanceList& cl)` | 批量添加 |
| `TypeConformanceList& remove(const TypeConformanceList& cl)` | 批量移除 |

---

### `SlangCompilerFlags`（枚举）

- **职责**: 定义 Slang 编译器标志

| 值 | 说明 |
|----|------|
| `None` | 无标志 |
| `TreatWarningsAsErrors` | 将警告视为错误 |
| `DumpIntermediates` | 转储编译中间产物 |
| `FloatingPointModeFast` | 快速浮点模式 |
| `FloatingPointModePrecise` | 精确浮点模式 |
| `GenerateDebugInfo` | 生成调试信息 |
| `MatrixLayoutColumnMajor` | 列主序矩阵布局（默认行主序） |

---

### `ProgramDesc`

- **继承**: 无
- **职责**: 描述待创建的着色器程序，包括着色器模块、入口点组、类型一致性和编译选项

#### 内部类型

| 类型 | 说明 |
|------|------|
| `ShaderID` | 着色器标识符，用于绑定表中引用入口点组 |
| `ShaderSource` | 单个着色器源码（文件或字符串） |
| `ShaderModule` | 着色器模块，对应一个翻译单元 |
| `EntryPoint` | 单个入口点（类型、名称、导出名称） |
| `EntryPointGroup` | 入口点组，主要用于光线追踪 Hit Group |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `ShaderModule& addShaderModule(std::string name = {})` | 添加空着色器模块 |
| `ProgramDesc& addShaderLibrary(std::filesystem::path path)` | 从文件添加着色器库 |
| `EntryPointGroup& addEntryPointGroup(uint32_t shaderModuleIndex = -1)` | 添加入口点组 |
| `ProgramDesc& vsEntry/hsEntry/dsEntry/gsEntry/psEntry/csEntry(const std::string& name)` | 快捷添加各阶段入口点 |
| `ShaderID addRayGen(...)` | 添加光线生成着色器 |
| `ShaderID addMiss(...)` | 添加未命中着色器 |
| `ShaderID addHitGroup(...)` | 添加命中组（最近命中/任意命中/相交） |
| `void finalize()` | 计算所有入口点的全局索引 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `shaderModules` | `std::vector<ShaderModule>` | 着色器模块列表 |
| `entryPointGroups` | `std::vector<EntryPointGroup>` | 入口点组列表 |
| `typeConformances` | `TypeConformanceList` | 全局类型一致性 |
| `shaderModel` | `ShaderModel` | 着色器模型版本 |
| `compilerFlags` | `SlangCompilerFlags` | 编译器标志 |
| `maxTraceRecursionDepth` | `uint32_t` | 最大光线追踪递归深度 |
| `maxPayloadSize` | `uint32_t` | 最大光线负载大小（字节） |
| `maxAttributeSize` | `uint32_t` | 最大属性大小（字节） |
| `rtPipelineFlags` | `RtPipelineFlags` | 光线追踪管线状态标志 |

---

### `Program`

- **继承**: `Object`
- **职责**: 着色器程序的高层抽象，管理不同宏定义/类型一致性下的多个程序版本

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<Program> create(ref<Device>, ProgramDesc, DefineList)` | 创建新程序 |
| `static ref<Program> createCompute(...)` | 从文件创建计算程序 |
| `static ref<Program> createGraphics(...)` | 从文件创建图形程序 |
| `const ref<const ProgramVersion>& getActiveVersion() const` | 获取当前活跃版本（触发懒编译） |
| `bool addDefine(const std::string& name, const std::string& value = "")` | 添加宏定义 |
| `bool addDefines(const DefineList& dl)` | 批量添加宏定义 |
| `bool removeDefine(const std::string& name)` | 移除宏定义 |
| `bool removeDefines(const DefineList& dl)` | 批量移除宏定义 |
| `bool setDefines(const DefineList& dl)` | 设置完整宏定义列表 |
| `bool addTypeConformance(...)` | 添加类型一致性 |
| `bool removeTypeConformance(...)` | 移除类型一致性 |
| `bool setTypeConformances(const TypeConformanceList&)` | 设置完整类型一致性列表 |
| `const ref<const ProgramReflection>& getReflector() const` | 获取反射信息 |
| `ref<RtStateObject> getRtso(RtProgramVars* pVars)` | 获取光线追踪状态对象 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `BreakableReference<Device>` | GPU 设备引用 |
| `mDesc` | `ProgramDesc` | 程序描述 |
| `mDefineList` | `DefineList` | 当前宏定义列表 |
| `mTypeConformanceList` | `TypeConformanceList` | 当前类型一致性列表 |
| `mProgramVersions` | `std::map<ProgramVersionKey, ref<const ProgramVersion>>` | 已编译版本缓存 |
| `mpActiveVersion` | `ref<const ProgramVersion>` | 当前活跃版本 |
| `mRtsoGraph` | `StateGraph<ref<RtStateObject>, void*>` | 光线追踪状态对象图 |

## 依赖关系

### 本文件引用

- `ProgramVersion.h` — 程序版本
- `DefineList.h` — 宏定义列表
- `Core/Object.h` — 基类
- `Core/API/Raytracing.h`, `Core/API/RtStateObject.h` — 光线追踪相关
- `Core/State/StateGraph.h` — 状态图
- `ProgramManager.h` — 程序编译管理（.cpp）
- `ProgramVars.h` — 程序变量（.cpp）

### 被以下文件引用

- `ProgramManager.h/.cpp` — 编译和链接程序
- `ProgramVars.h/.cpp` — 创建程序变量
- `ProgramVersion.h/.cpp` — 程序版本管理
- `RtBindingTable.h` — 光线追踪绑定表

## 实现细节

- **懒编译策略**: 宏定义或类型一致性的任何修改只会标记 `mLinkRequired = true`，实际编译推迟到 `getActiveVersion()` 调用时执行
- **版本缓存**: 使用 `(DefineList, TypeConformanceList)` 组合作为键缓存已编译的 `ProgramVersion`，避免重复编译
- **光线追踪状态对象图**: 使用 `StateGraph` 缓存和复用 `RtStateObject`，在 `getRtso()` 中根据当前 `ProgramKernels` 查找或创建
- **文件修改检测**: `checkIfFilesChanged()` 追踪所有依赖文件的修改时间，支持热重载
- **入口点验证**: 构造时检查导出名称唯一性，并对光线追踪程序强制要求设置递归深度和负载大小
- **Python 绑定**: 通过 `FALCOR_SCRIPT_BINDING` 暴露 `ProgramDesc`、`Program` 及其方法到 Python
