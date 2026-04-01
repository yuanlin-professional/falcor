# RtAccelerationStructurePostBuildInfoPool 源码文档

> 路径: `Source/Falcor/Core/API/RtAccelerationStructurePostBuildInfoPool.h` / `RtAccelerationStructurePostBuildInfoPool.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

管理光线追踪加速结构的构建后信息查询池，用于查询加速结构的压缩后大小、序列化大小或当前大小。

## 类与接口

### `RtAccelerationStructurePostBuildInfoQueryType`（枚举）
| 值 | 说明 |
|----|------|
| `CompactedSize` | 压缩后大小 |
| `SerializationSize` | 序列化大小 |
| `CurrentSize` | 当前大小 |

### `RtAccelerationStructurePostBuildInfoPool`
- **继承**: `Object`
- **职责**: 管理加速结构构建后信息查询

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `static ref<...> create(Device*, const Desc&)` | 创建查询池 |
| `uint64_t getElement(CopyContext*, uint32_t index)` | 获取查询结果 |
| `void reset(CopyContext*)` | 重置查询池 |

### `RtAccelerationStructurePostBuildInfoDesc`（结构体）
| 成员 | 说明 |
|------|------|
| `type` | 查询类型 |
| `pool` | 查询池指针 |
| `index` | 查询池中的索引 |

## 依赖关系
### 本文件引用
- `Core/Macros.h`, `Core/Object.h`, `slang-gfx.h`

### 被以下文件引用
- `RtAccelerationStructure.h`, `RenderContext.h`
