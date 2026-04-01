# HitInfo 源码文档

> 路径: `Source/Falcor/Scene/HitInfo.h` + `Source/Falcor/Scene/HitInfo.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene

## 功能概述

HitInfo 是主机端（CPU 端）的工具类，用于为 GPU 端的命中信息（HitInfo）设置位分配方案。它根据场景中的几何体实例数量和图元数量，计算编码命中信息所需的位数，并提供着色器宏定义和资源格式。

默认情况下，HitInfo 编码为 128 位（`RGBA32Uint`）。在仅包含三角形网格且场景足够小的情况下，支持压缩模式（64 位，`RG32Uint`），此时重心坐标被量化为 16 位 unorm。

## 类与接口

### `HitInfo`

- **继承**: 无
- **职责**: 管理光线命中信息的位编码方案，为 GPU 着色器提供必要的宏定义

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `HitInfo(const Scene& scene, bool useCompression = false)` | 构造函数，根据场景初始化位分配 |
| `void init(const Scene& scene, bool useCompression)` | 根据场景的几何体实例数和图元数计算位分配 |
| `DefineList getDefines() const` | 返回 GPU 着色器打包/解包 HitInfo 所需的宏定义列表 |
| `ResourceFormat getFormat() const` | 返回编码命中信息所需的资源格式（压缩时为 `RG32Uint`，否则为 `RGBA32Uint`） |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mUseCompression` | `bool` | 是否使用压缩格式（64 位） |
| `mTypeBits` | `uint32_t` | 存储命中类型所需的位数 |
| `mInstanceIDBits` | `uint32_t` | 存储几何实例 ID 所需的位数 |
| `mPrimitiveIndexBits` | `uint32_t` | 存储图元索引所需的位数 |

## 依赖关系

### 本文件引用

- `Core/Macros.h` - 基础宏定义
- `Core/API/Formats.h` - 资源格式定义（`ResourceFormat`）
- `Core/Program/DefineList.h` - 着色器宏定义列表
- `Scene.h` - 场景类，用于查询几何体信息
- `HitInfoType.slang` - 命中类型枚举定义
- `Utils/Logger.h` - 日志输出

### 被以下文件引用

- `Scene.h` - 场景类持有 HitInfo 实例
- `HitInfo.slang` - GPU 端对应的着色器实现

## 实现细节

- `init()` 方法遍历场景中的所有网格、曲线和 SDF 网格，找出最大图元数量，并通过 `allocateBits()` 计算所需位数
- 压缩模式要求：(1) 压缩后的头部位数 <= 32 位，(2) 场景仅包含三角形网格
- 压缩模式下类型字段固定使用 2 位（`kCompressedTypeBits`），足以编码 `None`、`Triangle` 和 `Volume` 三种类型
- 如果总位数超过 64 位（类型 + 实例ID > 32 位或图元索引 > 32 位），将抛出异常
