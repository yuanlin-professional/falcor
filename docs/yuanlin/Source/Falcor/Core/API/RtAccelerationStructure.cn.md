# RtAccelerationStructure 源码文档

> 路径: `Source/Falcor/Core/API/RtAccelerationStructure.h` / `RtAccelerationStructure.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

RtAccelerationStructure 模块定义了光线追踪加速结构相关的数据类型和接口。包括实例描述、几何描述、构建输入和加速结构对象本身。加速结构是光线追踪的核心数据结构，用于高效的射线-场景求交。

## 类与接口

### 枚举与标志
| 类型 | 说明 |
|------|------|
| `RtGeometryInstanceFlags` | 几何实例标志（剔除禁用、正面方向、强制不透明等） |
| `RtAccelerationStructureKind` | 加速结构类型（TopLevel、BottomLevel） |
| `RtAccelerationStructureBuildFlags` | 构建标志（AllowUpdate、AllowCompaction、PreferFastTrace、PreferFastBuild 等） |
| `RtGeometryType` | 几何类型（Triangles、ProcedurePrimitives） |
| `RtGeometryFlags` | 几何标志（Opaque、NoDuplicateAnyHitInvocation） |

### `RtInstanceDesc`（结构体）
- **职责**: 光线追踪实例描述，内存布局与 D3D12/Vulkan 一致

| 成员 | 说明 |
|------|------|
| `transform[3][4]` | 3x4 变换矩阵 |
| `instanceID` | 实例 ID（24 位） |
| `instanceMask` | 实例掩码（8 位） |
| `instanceContributionToHitGroupIndex` | Hit Group 索引贡献（24 位） |
| `flags` | 实例标志（8 位） |
| `accelerationStructure` | 底层加速结构的设备地址 |

### `RtAccelerationStructure`
- **继承**: `Object`
- **职责**: 管理光线追踪加速结构

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `static ref<RtAccelerationStructure> create(Device*, const BuildDesc&)` | 创建加速结构 |
| `DeviceAddress getGpuAddress()` | 获取 GPU 地址 |
| `uint64_t getCompactedSize(CopyContext*) const` | 获取压缩后大小 |
| `static void getPrebuildInfo(Device*, const BuildInputs&, RtAccelerationStructurePrebuildInfo&)` | 获取预构建信息 |

## 依赖关系
### 本文件引用
- `Buffer.h`, `Formats.h`, `ResourceViews.h`, `Utils/Math/Matrix.h`

### 被以下文件引用
- `RenderContext.h`, `ParameterBlock.h`
