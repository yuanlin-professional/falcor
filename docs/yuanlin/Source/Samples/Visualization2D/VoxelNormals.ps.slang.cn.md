# VoxelNormals.ps.slang 着色器文档

> 路径: `Source/Samples/Visualization2D/VoxelNormals.ps.slang`
> 类型: Slang 像素着色器
> 模块: Samples/Visualization2D

## 功能概述

体素法线可视化(Voxel Normals)场景的像素着色器。该着色器可视化一个 4x4 体素网格上的 SDF(有符号距离场)数据及其法线。左侧显示标准网格,右侧显示带有动态变形的扭曲网格。两个网格都支持显示 SDF 等值面(负值区域着蓝色)、法线场、网格线和对角线。鼠标点击可以在指定位置显示 SDF 法线向量。

## 结构体与接口

### 常量缓冲区 `Visual2DCB`

| 成员 | 类型 | 说明 |
|------|------|------|
| `iResolution` | `float2` | 渲染目标分辨率 |
| `iMousePosition` | `float2` | 鼠标位置(屏幕坐标) |
| `iGlobalTime` | `float` | 全局时间(秒) |

### 常量缓冲区 `VoxelNormalsCB`

| 成员 | 类型 | 说明 |
|------|------|------|
| `iShowNormalField` | `int` | 是否显示法线场 |
| `iShowBoxes` | `int` | 是否显示体素盒子边框 |
| `iShowBoxAroundPoint` | `int` | 是否显示鼠标点周围的盒子 |
| `iShowBoxDiagonals` | `int` | 是否显示盒子对角线 |
| `iShowBorderLines` | `int` | 是否显示边界线 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void evalBilinear(float2 barys, float s00, float s10, float s01, float s11, out float sdf)` | 双线性插值标量 SDF 值 |
| `void evalBilinear(float2 barys, float2 p00, float2 p10, float2 p01, float2 p11, out float2 point)` | 双线性插值 2D 点 |
| `bool isInsideTri(float2 coords, float2 _p00, float2 _p10, float2 _p01, out float2 barys)` | 判断点是否在三角形内并计算重心坐标 |
| `float2 normalVector(float2 uv, float _s00, float _s10, float _s01, float _s11)` | 计算双线性插值 SDF 的梯度法线 |
| `float2 computeSimpleNormal(float2 pos)` | 在标准网格上计算指定位置的 SDF 法线 |
| `float2 convertToDualVoxelSpaceIndex(float2 pos, out int2 voxelIndex)` | 将位置转换为对偶体素空间索引和局部坐标 |
| `void computeStandardPositionsAndNormals(float2 position, out float2 normal, out float2 warpedPosition, out float2 warpedNormal)` | 计算标准法线和扭曲后的位置及法线 |
| `void drawBox(...)` | 绘制单个体素盒子,包括 SDF 等值面、法线场、网格线和对角线 |
| `float4 main(float2 texC: TEXCOORD) : SV_TARGET` | 像素着色器入口 |

## 静态数据

| 数据 | 类型 | 说明 |
|------|------|------|
| `sdf[5][5]` | `float` | 5x5 网格顶点的预定义 SDF 值 |
| `P[5][5]` | `float2` | 标准网格顶点位置 |
| `X[5][5]` | `float2` | 扭曲网格顶点位置(动态计算) |

## 依赖关系 / import

| 模块 | 说明 |
|------|------|
| `Utils.Math.MathHelpers` | 数学辅助函数 |
| `Utils.SDF.SDF2DDraw` | SDF 2D 绘图函数 |
| `Utils.SDF.SDF2DPrimitives` | SDF 2D 基本图元(圆、线段、向量等) |

## 实现细节

- **SDF 数据**: 使用硬编码的 5x5 浮点数组作为 SDF 值,正值表示表面外部,负值表示内部。
- **网格构建**: 标准网格 `P` 为均匀间隔的正方形网格;扭曲网格 `X` 在标准网格基础上添加基于时间的正弦抖动变形。
- **法线计算**: 通过双线性插值 SDF 的解析梯度计算法线向量。对于扭曲网格,法线通过三角形仿射变换从标准空间映射到扭曲空间。
- **体素盒子渲染**: 每个体素单元被分割为两个三角形进行渲染。在三角形内部通过双线性插值 SDF 值判断内外(负值区域着蓝色高亮)。
- **交互**: 鼠标点击位置显示红色法线向量箭头,同时在标准网格和扭曲网格上对应显示。
- **可视化选项**: 通过常量缓冲区控制网格线、对角线、法线场、边界线等元素的可见性。
- 坐标系:X 向右,Y 向上,归一化到 `iResolution.x`。
