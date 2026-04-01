# ConvertedMaterialCache 源码文档

> 路径: `Source/Modules/USDUtils/ConvertedMaterialCache.h`, `Source/Modules/USDUtils/ConvertedMaterialCache.cpp`
> 类型: C++ 头文件 + 实现
> 模块: USDUtils

## 功能概述

线程安全的材质缓存模板类，用于避免将相同的 `UsdShadeShader` 重复转换为 Falcor 材质实例。通过双重缓存机制（按 Prim 缓存和按材质规格缓存）实现高效去重。`.cpp` 文件仅包含头文件，所有实现均在头文件中以模板形式提供。

## 类与接口

### `ConvertedMaterialCache<T, S, H>`

- **模板参数**:
  - `T`: 材质类型（如 `Falcor::StandardMaterial`）
  - `S`: 材质规格类型（如 `StandardMaterialSpec`）
  - `H`: 规格类型的哈希类
- **职责**: 缓存已转换的材质，支持按 USD Prim 和按材质规格两种方式查找，避免创建重复材质。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `ref<T> get(const UsdShadeShader& shader)` | 按 Prim 查找缓存的材质。若正在转换中则阻塞等待；若不存在则插入占位条目并返回 nullptr |
| `ref<T> get(const S& spec, const std::string& shaderName)` | 按材质规格查找缓存。行为与按 Prim 查找类似 |
| `void add(const UsdShadeShader& shader, const S& spec, ref<T> pMaterial)` | 同时更新按 Prim 和按规格的缓存，并通知所有等待线程 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mPrimMaterialCache` | `unordered_map<UsdPrim, ref<T>>` | 按 Prim 索引的缓存；nullptr 值表示正在转换中 |
| `mSpecMaterialCache` | `unordered_map<S, ref<T>, H>` | 按材质规格索引的缓存 |
| `mCacheMutex` | `std::mutex` | 保护缓存的互斥锁 |
| `mPrimCacheUpdated` | `std::condition_variable` | Prim 缓存更新通知 |
| `mSpecCacheUpdated` | `std::condition_variable` | 规格缓存更新通知 |

## 依赖关系

### 本文件引用

- `Scene/Material/Material.h` - 材质基类
- `USDUtils/USDUtils.h` - `UsdObjHash`
- `USDUtils/USDHelpers.h` - USD 警告宏

### 被以下文件引用

- `PreviewSurfaceConverter/PreviewSurfaceConverter.h` - 作为 `mMaterialCache` 成员使用

## 实现细节

- **线程安全设计**: 使用互斥锁 + 条件变量实现生产者-消费者模式。首个请求特定材质的线程负责转换，其他请求相同材质的线程会阻塞等待。
- **双重缓存**: 先按 Prim 快速查找（避免不必要的规格构建），再按规格查找（检测不同 Prim 但参数相同的材质）。
- **约定**: 调用 `get` 后必须最终调用 `add`，否则等待线程将永久阻塞。
