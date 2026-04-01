# LightBVHRefit.cs.slang 源码文档

> 路径: `Source/Falcor/Rendering/Lights/LightBVHRefit.cs.slang`
> 类型: Slang 计算着色器
> 模块: Rendering/Lights

## 功能概述

本计算着色器实现了光源 BVH 的 GPU 端 refit（重新拟合）操作。当场景中的发光几何体发生变换（如动画）但拓扑不变时，可以仅更新每个节点的包围盒和法线包围锥，而无需重建整个 BVH 层级结构。包含两个内核：`updateLeafNodes` 更新叶节点，`updateInternalNodes` 自底向上更新内部节点。

## 结构体与接口

### 常量缓冲区 `CB`

| 成员 | 类型 | 说明 |
|------|------|------|
| `gLights` | `LightCollection` | 光源集合 |
| `gLightBVH` | `RWLightBVH` | 可写 BVH |
| `gNodeIndices` | `StructuredBuffer<uint>` | 按深度排序的节点索引 |
| `gFirstNodeOffset` | `uint` | 当前批次在 `gNodeIndices` 中的起始偏移 |
| `gNodeCount` | `uint` | 当前批次需要处理的节点数量 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `[numthreads(256, 1, 1)] void updateLeafNodes(uint3 DTid : SV_DispatchThreadID)` | 更新叶节点的 AABB 和法线包围锥 |
| `float sinFromCos(float cosAngle)` | 辅助函数，从 cos 值计算 sin 值 |
| `[numthreads(256, 1, 1)] void updateInternalNodes(uint3 DTid : SV_DispatchThreadID)` | 更新内部节点的 AABB 和法线包围锥 |

## 实现细节

### `updateLeafNodes`

1. 遍历叶节点中的所有三角形，累计顶点位置更新 AABB
2. 累加三角形法线，计算平均方向作为锥方向
3. 如果法线和向量有效，遍历所有三角形找到最大偏离角，设置 `cosConeAngle`
4. 无效时设置为 `kInvalidCosConeAngle`

### `updateInternalNodes`

1. 获取左子节点（紧邻当前节点）和右子节点（通过索引引用）的属性
2. 取左右子节点 AABB 的并集更新当前节点 AABB
3. 将左右子节点的锥方向相加，计算新锥方向
4. 使用旋转公式合并两个子节点的法线包围锥角
5. 如果合并角度超过 pi，标记锥为无效

### 执行顺序

由 CPU 端 `LightBVH::refit()` 控制：先执行所有叶节点更新，再从最深内部层逐层向上更新内部节点。

## 依赖关系

### import 引用

- `Utils/Math/MathConstants.slangh` — 数学常量（FLT_MAX、FLT_MIN、kInvalidCosConeAngle）
- `Scene.Lights.LightCollection` — 光源集合数据
- `Rendering.Lights.LightBVH` — BVH 数据结构（RWLightBVH）
