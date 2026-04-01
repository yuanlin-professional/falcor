# ProgramManager.h / ProgramManager.cpp 源码文档

> 路径: `Source/Falcor/Core/Program/ProgramManager.h`, `Source/Falcor/Core/Program/ProgramManager.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/Program

## 功能概述

管理着色器程序的编译、链接和重载。`ProgramManager` 是 Falcor 中着色器编译的核心管理器，负责通过 Slang 编译器创建程序版本（`ProgramVersion`）和程序内核（`ProgramKernels`），并维护全局编译选项。每个 `Device` 实例拥有一个 `ProgramManager`。

## 类与接口

### `ProgramManager`

- **继承**: 无
- **职责**: 管理着色器程序的编译生命周期，包括 Slang 编译请求的创建、全局宏定义管理、强制编译标志、热重载和编译统计

#### 内部类型

| 类型 | 说明 |
|------|------|
| `ForcedCompilerFlags` | 强制启用/禁用的编译标志对 |
| `CompilationStats` | 编译统计信息（版本/内核数量、时间） |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `ProgramManager(Device* pDevice)` | 构造函数，初始化全局宏定义（如 NVAPI 可用性） |
| `ref<const ProgramVersion> createProgramVersion(const Program&, std::string& log) const` | 编译程序并创建版本，执行 Slang 前端编译和反射 |
| `ref<const ProgramKernels> createProgramKernels(const Program&, const ProgramVersion&, const ProgramVars&, std::string& log) const` | 根据特化参数创建程序内核，处理类型一致性 |
| `ref<const EntryPointGroupKernels> createEntryPointGroupKernels(...) const` | 为入口点组创建内核对象 |
| `void registerProgramForReload(Program*)` | 注册程序以支持热重载 |
| `void unregisterProgramForReload(Program*)` | 取消注册 |
| `bool reloadAllPrograms(bool forceReload = false)` | 重载所有已修改的程序 |
| `void addGlobalDefines(const DefineList&)` | 添加全局宏定义（触发全部重编译） |
| `void removeGlobalDefines(const DefineList&)` | 移除全局宏定义（触发全部重编译） |
| `void setGlobalCompilerArguments(const std::vector<std::string>&)` | 设置全局编译器参数 |
| `void setGenerateDebugInfoEnabled(bool)` | 启用/禁用全局调试信息生成 |
| `void setForcedCompilerFlags(ForcedCompilerFlags)` | 设置强制编译标志 |
| `std::string getHlslLanguagePrelude() const` | 获取 HLSL 语言前缀 |
| `void setHlslLanguagePrelude(const std::string&)` | 设置 HLSL 语言前缀 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `Device*` | 关联的 GPU 设备 |
| `mLoadedPrograms` | `std::vector<Program*>` | 已注册的程序列表 |
| `mCompilationStats` | `CompilationStats` | 编译统计数据 |
| `mGlobalDefineList` | `DefineList` | 全局宏定义 |
| `mGlobalCompilerArguments` | `std::vector<std::string>` | 全局编译器参数 |
| `mGenerateDebugInfo` | `bool` | 是否生成调试信息 |
| `mForcedCompilerFlags` | `ForcedCompilerFlags` | 强制编译标志 |
| `mHitGroupID` | `uint32_t` | 命中组自增 ID 计数器 |

## 依赖关系

### 本文件引用

- `Program.h` — 程序定义
- `Core/API/Device.h` — 设备访问和 Slang 全局会话
- `Core/Platform/OS.h` — 文件系统操作
- `Utils/Logger.h` — 日志
- `Utils/Timing/CpuTimer.h` — 编译计时
- `<slang.h>` — Slang 编译器 API

### 被以下文件引用

- `Program.cpp` — 通过设备获取 ProgramManager 进行编译和注册
- `ProgramVersion.cpp` — 调用 `createProgramKernels`
- `Core/API/Device.h` — 设备持有 ProgramManager 实例

## 实现细节

- **Slang 编译请求构建** (`createSlangCompileRequest`): 该私有方法是编译流程的核心，负责：
  - 配置搜索路径、目标格式（DXIL/SPIRV）、着色器模型 Profile
  - 合并全局和程序级宏定义
  - 设置浮点模式、矩阵布局（行主序/列主序）
  - 添加翻译单元和入口点
  - 处理 NVAPI 包含路径
  - 禁用短路求值等 Slang 兼容性选项

- **程序版本创建** (`createProgramVersion`): 执行 Slang 前端编译（不生成代码），提取入口点和依赖文件列表，并进行反射分析

- **程序内核创建** (`createProgramKernels`): 处理类型一致性特化，为每个入口点组创建复合组件类型，重新执行反射以获得特化后的绑定信息，最终通过 GFX API 创建着色器程序

- **入口点组分类**: `createEntryPointGroupKernels` 根据着色器类型自动分类为 Rasterization、Compute、RtSingleShader 或 RtHitGroup

- **热重载**: `reloadAllPrograms` 遍历所有注册程序，检查依赖文件修改时间，对变化的程序执行重置
