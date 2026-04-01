# DisplacementUpdate.cs.slang 源码文档

> 路径: `Source/Falcor/Scene/Displacement/DisplacementUpdate.cs.slang`
> 类型: Slang 计算着色器 (Compute Shader)
> 模块: Scene/Displacement

## 功能概述

计算着色器，用于为位移映射的三角形计算轴对齐包围盒（AABB）。该着色器在 GPU 上并行处理大量三角形，根据位移数据计算每个三角形经位移后的包围盒。这些 AABB 用于构建加速结构（BVH），使光线追踪能够高效地对位移几何体进行求交测试。

## 静态常量

| 常量 | 类型 | 值 | 说明 |
|------|------|-----|------|
| `kUsePreciseShellBounds` | `bool` | `true` | 是否使用精确的外壳包围盒。为 `true` 时基于三角形局部的位移范围计算紧凑 AABB；为 `false` 时使用全局最大位移扩展基础 AABB。 |

## 常量缓冲区（cbuffer）

### `CB`

| 成员 | 类型 | 说明 |
|------|------|------|
| `gTaskCount` | `uint` | 任务总数 |
| `gTasks` | `StructuredBuffer<DisplacementUpdateTask>` | 任务描述数组（只读） |
| `gAABBs` | `RWStructuredBuffer<AABB>` | 输出 AABB 数组（读写） |

## 函数

### `main`（入口点）

```
[numthreads(256, 1, 1)]
void main(uint3 dispatchThreadId : SV_DispatchThreadID)
```

| 参数 | 说明 |
|------|------|
| `dispatchThreadId.x` | 线程索引（对应任务内的工作线程） |
| `dispatchThreadId.y` | 任务索引（对应不同的 DisplacementUpdateTask） |

**执行流程**:

1. 根据 `dispatchThreadId` 确定当前线程的任务索引和线程索引
2. 从任务中获取网格 ID，加载对应的位移数据（`DisplacementData`）
3. 计算当前线程需要处理的三角形迭代次数
4. 对每个三角形：
   - 通过索引获取三角形的三个顶点
   - 根据 `kUsePreciseShellBounds` 选择 AABB 计算策略：
     - **非精确模式**: 基于顶点位置计算基础 AABB，然后向外扩展全局最大位移量
     - **精确模式**: 使用 `getShellMinMax` 获取三角形局部的位移范围，沿顶点法线方向分别按最小/最大高度挤出，计算更紧凑的 AABB
5. 将计算结果写入 `gAABBs` 对应位置

## 依赖关系

### import

- `Scene.Scene`：场景数据访问（`gScene`），提供网格、顶点、索引等数据
- `Scene.Displacement.DisplacementMapping`：位移映射扩展方法（`getShellMinMax`、`getConservativeGlobalExpansion` 等）
- `Utils.Math.AABB`：轴对齐包围盒结构体
- `DisplacementUpdateTask`：任务描述结构体

### 被以下文件引用

- CPU 端的位移更新管理器：调度该计算着色器并传入任务数据

## 实现细节

### 任务调度模型

- 工作被组织为多个 `DisplacementUpdateTask`，每个任务处理一个网格中的一段连续三角形
- 每个任务固定启动 `kThreadCount`（16384）个线程
- 线程组大小为 256 x 1 x 1，即每个任务需要 64 个线程组
- 每个线程按固定步长（kThreadCount）循环处理多个三角形

### 精确外壳包围盒计算

当 `kUsePreciseShellBounds = true` 时：
- 调用 `displacementData.getShellMinMax()` 获取三角形 UV 区域内的保守位移范围 `[minHeight, maxHeight]`
- 对每个顶点，沿法线方向按 `minHeight` 和 `maxHeight` 分别偏移
- 将所有 6 个偏移后的点纳入 AABB 计算
- 这比简单的全局扩展产生更紧凑的包围盒，显著提升光线追踪效率

### 非精确模式

当 `kUsePreciseShellBounds = false` 时：
- 仅基于原始顶点位置计算 AABB
- 向各方向均匀扩展 `globalExpansion`（全局最大位移绝对值）
- 简单但包围盒可能过大，降低求交效率
