# LightBVHSampler.slang 源码文档

> 路径: `Source/Falcor/Rendering/Lights/LightBVHSampler.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Lights

## 功能概述

本着色器文件实现了基于光源 BVH 的自发光光源采样器的 GPU 端逻辑。采样过程分为两个阶段：（1）随机遍历 BVH 树选择叶节点，在每个内部节点处根据子节点的重要性概率选择左或右子节点；（2）在叶节点内选择具体的三角形。支持多种可配置的剔除和约束方法，包括包围锥（bounding cone）约束 NdotL、光照锥（lighting cone）剔除背面光源、以及多种立体角约束方法。同时支持 PDF 评估，通过遍历位掩码回溯采样路径。

## 结构体与接口

### `LightBVHSampler`

- **实现**: `IEmissiveLightSampler`
- **职责**: 使用 BVH 遍历进行自发光光源的重要性采样

#### 编译时常量

| 常量 | 说明 |
|------|------|
| `kUseBoundingCone` | 是否使用包围锥约束 NdotL |
| `kUseLightingCone` | 是否使用光照锥剔除背面光源 |
| `kDisableNodeFlux` | 是否禁用节点通量权重 |
| `kUseUniformTriangleSampling` | 叶节点内是否均匀选择三角形 |
| `kActualMaxTrianglesPerNode` | 每叶节点最大三角形数 |
| `kSolidAngleBoundMethod` | 立体角约束方法 |

| 成员 | 类型 | 说明 |
|------|------|------|
| `_lightBVH` | `LightBVH` | BVH 数据结构 |

#### 公共函数

| 函数签名 | 说明 |
|----------|------|
| `bool sampleLight<S>(...)` | 通过 BVH 遍历选择三角形，再在三角形上均匀采样 |
| `float evalTriangleSelectionPdf(...)` | 评估选择指定三角形的概率（遍历 PDF * 叶节点内选择 PDF） |
| `float evalPdf(...)` | 评估完整 PDF（三角形选择 PDF * 面积采样 PDF） |

#### 私有函数

| 函数签名 | 说明 |
|----------|------|
| `float cosSubClamped(...)` | 计算 cos(max(0, a-b))，使用正弦余弦加法公式 |
| `float sinSubClamped(...)` | 计算 sin(max(0, a-b)) |
| `float boundCosineTerm(...)` | 对 AABB 计算 NdotL 的保守上界，支持三种方法（Sphere/BoxToCenter/BoxToAverage） |
| `float computeImportance(...)` | 计算从着色点看某节点的重要性 = (flux * NdotL * orientationWeight) / distance^2 |
| `bool traverseTree(...)` | 自顶向下遍历 BVH，在每个内部节点按重要性比率随机选择子节点 |
| `float computeTriangleImportance(...)` | 计算单个三角形从着色点看的重要性 |
| `bool pickTriangle(...)` | 在叶节点中选择三角形（均匀或按重要性） |
| `bool sampleLightViaBVH(...)` | 完整采样流程：遍历 + 选择三角形 |
| `float evalBVHTraversalPdf(...)` | 使用位掩码回溯遍历路径，评估遍历 PDF |
| `float evalNodeSamplingPdf(...)` | 评估叶节点内三角形选择的 PDF |

## 实现细节

### 重要性计算 (`computeImportance`)

1. 获取节点属性（通量、位置、范围、锥方向/角度）
2. 如果启用包围锥，使用 `boundCosineTerm` 计算 NdotL 保守上界
3. 如果启用光照锥，计算方向权重：cos(max(0, theta - coneAngle - thetaBoundingCone))
4. 距离夹取为至少半个节点半径，避免近距离过估
5. 最终重要性 = (flux * NdotL * orientationWeight) / distance^2

### BVH 遍历 (`traverseTree`)

- 使用随机数 `u` 的重缩放技术（rescaling）：选择左子时 `u = u / pLeft`，选择右子时 `u = (u - pLeft) / pRight`
- 这使得到达叶节点后 `u` 仍然是均匀分布的，可直接用于三角形选择

### PDF 评估 (`evalBVHTraversalPdf`)

- 使用每个三角形在 BVH 构建时记录的遍历位掩码
- 从根节点开始，每层检查位掩码的最低位决定进入左子（0）还是右子（1）
- 累乘各层的选择概率得到总遍历 PDF

## 依赖关系

### import 引用

- `Utils/Math/MathConstants.slangh` — 数学常量
- `Scene.Scene` — 场景全局变量
- `Utils.Math.MathHelpers` — 数学辅助函数
- `Utils.Geometry.GeometryHelpers` — 几何辅助函数（最小距离计算等）
- `Utils.Sampling.SampleGeneratorInterface` — 样本生成器接口
- `Rendering.Lights.LightBVH` — BVH 数据结构
- `Rendering.Lights.LightBVHSamplerSharedDefinitions` — 共享枚举定义
- `Rendering.Lights.EmissiveLightSamplerHelpers` — 三角形采样辅助函数
- `Rendering.Lights.EmissiveLightSamplerInterface` — 采样器接口定义
