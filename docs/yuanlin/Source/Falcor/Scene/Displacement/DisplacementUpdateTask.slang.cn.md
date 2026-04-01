# DisplacementUpdateTask.slang 源码文档

> 路径: `Source/Falcor/Scene/Displacement/DisplacementUpdateTask.slang`
> 类型: Slang 着色器头文件（主机/设备共享）
> 模块: Scene/Displacement

## 功能概述

定义位移更新任务的数据结构 `DisplacementUpdateTask`，用于描述一个 GPU 计算任务：为指定网格中的一段连续三角形计算位移后的 AABB。该结构体在 CPU 端填充，在 GPU 端（`DisplacementUpdate.cs.slang`）消费，是主机与设备之间的共享数据结构。

## 结构体与接口

### `DisplacementUpdateTask`

描述一个位移 AABB 更新任务。

#### 静态常量

| 常量 | 类型 | 值 | 说明 |
|------|------|-----|------|
| `kThreadCount` | `uint` | `16384` | 每个任务启动的 GPU 线程数。决定了单个任务的并行度和处理能力。 |

#### 成员变量

| 成员 | 类型 | 说明 |
|------|------|------|
| `meshID` | `uint` | 网格 ID，用于从场景中读取顶点数据 |
| `triangleIndex` | `uint` | 起始三角形索引，指定任务处理的第一个三角形在网格中的位置 |
| `AABBIndex` | `uint` | 起始 AABB 索引，指定输出 AABB 数组中的写入起始位置 |
| `count` | `uint` | 需要处理的三角形数量 |

## 函数

无。该文件仅定义数据结构。

## 依赖关系

### import

- `Utils/HostDeviceShared.slangh`：主机/设备共享宏（`BEGIN_NAMESPACE_FALCOR`、`END_NAMESPACE_FALCOR`），确保结构体在 CPU 和 GPU 上具有相同的内存布局

### 被以下文件引用

- `DisplacementUpdate.cs.slang`：作为计算着色器的输入任务结构体
- CPU 端的位移管理器：创建并填充任务列表

## 实现细节

- 使用 `#pragma once` 防止重复包含
- 通过 `BEGIN_NAMESPACE_FALCOR` / `END_NAMESPACE_FALCOR` 宏包裹，确保在 Falcor 命名空间内
- `kThreadCount = 16384` 的选择平衡了 GPU 占用率和任务粒度：足够大以充分利用 GPU 并行性，同时不会因单个任务过大而导致负载不均
- 任务的设计允许将大网格拆分为多个任务，每个任务处理不超过 `kThreadCount` 个三角形，便于跨多个线程组分发
