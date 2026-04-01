# RtBindingTable.h / RtBindingTable.cpp 源码文档

> 路径: `Source/Falcor/Core/Program/RtBindingTable.h`, `Source/Falcor/Core/Program/RtBindingTable.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/Program

## 功能概述

描述光线追踪着色器的绑定关系。`RtBindingTable` 定义了 raygen、miss 和 hit group 着色器的映射关系，是 DXR/Vulkan 光线追踪着色器绑定表（Shader Binding Table）的高层抽象。用户负责为特定的 `Program` 和场景创建绑定表，然后用于构建 `RtProgramVars`。

## 类与接口

### `RtBindingTable`

- **继承**: `Object`
- **职责**: 管理光线追踪着色器的 raygen/miss/hit group 绑定映射

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<RtBindingTable> create(uint32_t missCount, uint32_t rayTypeCount, uint32_t geometryCount)` | 创建绑定表 |
| `void setRayGen(ShaderID shaderID)` | 设置光线生成着色器 |
| `void setMiss(uint32_t missIndex, ShaderID shaderID)` | 设置未命中着色器 |
| `void setHitGroup(uint32_t rayType, uint32_t geometryID, ShaderID shaderID)` | 设置命中组着色器 |
| `void setHitGroup(uint32_t rayType, const std::vector<uint32_t>& geometryIDs, ShaderID shaderID)` | 批量设置多个几何体的命中组 |
| `void setHitGroup(uint32_t rayType, const std::vector<GlobalGeometryID>& geometryIDs, ShaderID shaderID)` | 批量设置（使用 GlobalGeometryID） |
| `ShaderID getRayGen() const` | 获取光线生成着色器 ID |
| `ShaderID getMiss(uint32_t missIndex) const` | 获取未命中着色器 ID |
| `ShaderID getHitGroup(uint32_t rayType, uint32_t geometryID) const` | 获取命中组着色器 ID |
| `uint32_t getMissCount() const` | 获取 miss 着色器数量 |
| `uint32_t getRayTypeCount() const` | 获取光线类型数量 |
| `uint32_t getGeometryCount() const` | 获取几何体数量 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mMissCount` | `uint32_t` | miss 着色器数量 |
| `mRayTypeCount` | `uint32_t` | 光线类型数量 |
| `mGeometryCount` | `uint32_t` | 场景中几何体数量 |
| `mShaderTable` | `std::vector<ShaderID>` | 着色器 ID 表（线性存储） |

## 依赖关系

### 本文件引用

- `Program.h` — `ProgramDesc::ShaderID` 类型定义
- `Core/Object.h` — 基类
- `Scene/SceneIDs.h` — `GlobalGeometryID` 类型
- `Core/Error.h` — 错误检查（.cpp）

### 被以下文件引用

- `ProgramVars.h` / `ProgramVars.cpp` — `RtProgramVars` 构造使用绑定表

## 实现细节

- **线性表存储**: 所有着色器 ID 存储在单一的 `mShaderTable` 向量中，布局为：
  - 索引 0: raygen 着色器
  - 索引 1 ~ missCount: miss 着色器
  - 索引 1+missCount ~ 末尾: hit group（按 `geometryID * rayTypeCount + rayType` 排列）

- **API 限制检查**: 构造时检查 DXR 规范限制：
  - miss 着色器最多 65536 个（2^16）
  - 光线类型最多 16 个（2^4）
  - 总表大小不超过 `uint32_t` 上限

- **默认值**: 所有条目初始化为无效的 `ShaderID`（`groupIndex = -1`），未绑定的槽位在着色器表中表示为空

- **GlobalGeometryID 支持**: 提供 `GlobalGeometryID` 重载，自动调用 `.get()` 转换为 `uint32_t`，方便与场景系统集成
