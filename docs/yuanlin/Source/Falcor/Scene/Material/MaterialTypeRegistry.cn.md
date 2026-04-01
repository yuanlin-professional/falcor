# MaterialTypeRegistry 源码文档
> 路径: `Source/Falcor/Scene/Material/MaterialTypeRegistry.h` + `MaterialTypeRegistry.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Material

## 功能概述
材质类型注册表，管理所有内置和动态注册的材质类型。提供线程安全的材质类型注册、名称查询和参数布局获取功能。支持在运行时动态注册新的材质类型。

## 类与接口

### 自由函数
| 函数签名 | 说明 |
|----------|------|
| `MaterialType registerMaterialType(string typeName)` | 注册新材质类型（线程安全） |
| `string to_string(MaterialType type)` | 获取材质类型名称（线程安全） |
| `size_t getMaterialTypeCount()` | 获取已注册材质类型总数 |
| `MaterialParamLayout getMaterialParamLayout(MaterialType type)` | 获取材质参数布局（用于可微渲染） |

### 内部类 `MaterialTypeRegistry`（匿名命名空间）
- **职责**: 单例注册表，维护类型-名称双向映射

## 依赖关系
### 本文件引用
- `MaterialTypes.slang` - 材质类型枚举
- `MaterialParamLayout.h` - 参数布局
- `StandardMaterialParamLayout.slang`, `PBRTDiffuseMaterialParamLayout.slang`, `PBRTConductorMaterialParamLayout.slang`

## 实现细节
- 内置材质类型在构造时预注册（Standard, Cloth, Hair, MERL, MERLMix, PBRT系列, RGL）
- 动态注册的类型 ID 从 `BuiltinCount + 1` 开始递增
- 类型 ID 不能超过 `MaterialHeader::kMaterialTypeBits` 位所能表示的最大值
- 提供 `fmt::formatter` 特化，支持材质类型的格式化输出
- 目前仅 Standard、PBRTDiffuse、PBRTConductor 支持参数布局
