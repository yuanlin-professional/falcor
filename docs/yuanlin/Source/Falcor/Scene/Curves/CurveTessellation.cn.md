# CurveTessellation 源码文档

> 路径: `Source/Falcor/Scene/Curves/CurveTessellation.h` + `CurveTessellation.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Curves

## 功能概述

提供将三次 B 样条曲线（Cubic B-Spline）转换为可渲染几何体的功能。支持两种转换模式：

1. **线性扫掠球（Linear Swept Sphere）**：将曲线转换为一系列线性球体段，适用于光线追踪中的曲线求交。
2. **多边形管道（PolyTube）**：将曲线细分为三角网格管道，适用于光栅化渲染。

两种模式均支持控制点细分、链抽稀、宽度缩放、纹理坐标插值等功能。

## 类与接口

### `CurveTessellation`

- **继承**: 无
- **职责**: 提供静态方法，将曲线控制点数据转换为不同类型的可渲染几何数据。该类不可实例化（构造函数为 `private`，且禁止拷贝）。

#### 内部结构体

##### `SweptSphereResult`

存储线性扫掠球转换的结果数据。

| 成员 | 类型 | 说明 |
|------|------|------|
| `degree` | `uint32_t` | 多项式阶数（当前仅支持线性，即 degree=1） |
| `indices` | `fast_vector<uint32_t>` | 球体段索引数组 |
| `points` | `fast_vector<float3>` | 球心位置数组 |
| `radius` | `fast_vector<float>` | 球体半径数组 |
| `texCrds` | `fast_vector<float2>` | 纹理坐标数组 |

##### `MeshResult`

存储多边形管道细分的网格结果数据。

| 成员 | 类型 | 说明 |
|------|------|------|
| `vertices` | `fast_vector<float3>` | 顶点位置数组 |
| `normals` | `fast_vector<float3>` | 法线数组 |
| `tangents` | `fast_vector<float4>` | 切线数组（w 分量为手性标志） |
| `texCrds` | `fast_vector<float2>` | 纹理坐标数组 |
| `radii` | `fast_vector<float>` | 各顶点对应的曲线半径 |
| `faceVertexCounts` | `fast_vector<uint32_t>` | 每个面的顶点数（均为 3，即三角形） |
| `faceVertexIndices` | `fast_vector<uint32_t>` | 面的顶点索引数组 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static SweptSphereResult convertToLinearSweptSphere(uint32_t strandCount, const uint32_t* vertexCountsPerStrand, const float3* controlPoints, const float* widths, const float2* UVs, uint32_t degree, uint32_t subdivPerSegment, uint32_t keepOneEveryXStrands, uint32_t keepOneEveryXVerticesPerStrand, float widthScale, const float4x4& xform)` | 将三次 B 样条曲线转换为线性扫掠球段。支持预变换（xform）、链抽稀、顶点抽稀和宽度缩放。 |
| `static MeshResult convertToPolytube(uint32_t strandCount, const uint32_t* vertexCountsPerStrand, const float3* controlPoints, const float* widths, const float2* UVs, uint32_t subdivPerSegment, uint32_t keepOneEveryXStrands, uint32_t keepOneEveryXVerticesPerStrand, float widthScale, uint32_t pointCountPerCrossSection)` | 将三次 B 样条曲线细分为三角网格管道。通过在横截面采样 `pointCountPerCrossSection` 个点生成管状网格。 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| 构造函数 | `private` | 禁止实例化，所有方法为静态方法 |
| 拷贝构造 / 赋值 | `deleted` | 禁止拷贝 |

## 实现文件内部结构体与辅助函数

### 内部结构体（`CurveTessellation.cpp`）

| 结构体 | 说明 |
|--------|------|
| `StrandArrays` | 存储单条曲线链的控制点、宽度、UV 数据及顶点计数 |
| `CurveArrays` | 对原始曲线数据数组的只读指针封装 |
| `CubicSplineCache` | 缓存三次样条插值器，避免重复构造。包含原始和优化后的样条对象 |

### 匿名命名空间辅助函数

| 函数 | 说明 |
|------|------|
| `transformSphere(xform, sphere)` | 对球体（中心+半径）施加 4x4 变换矩阵，假设各向同性缩放 |
| `sanitizeWidth(w)` | 确保曲线宽度不为零（最小值为 `float16_t::min`），用于区分曲线网格与原生网格 |
| `optimizeStrandGeometry(...)` | 优化曲线链几何：移除重复控制点，通过三次样条插值进行细分和抽稀 |
| `updateCurveFrame(strandArrays, fwd, s, t, j)` | 使用四元数旋转平滑更新曲线的正交坐标系（前向、侧向、法向），实现最小扭转帧 |
| `updateMeshResultBuffers(...)` | 在当前横截面位置生成圆形分布的网格顶点、法线、切线 |
| `connectFaceVertices(...)` | 连接相邻横截面的顶点，生成三角面片索引 |

### 常量

| 常量 | 值 | 说明 |
|------|-----|------|
| `kMeshCompensationScale` | `1.11f` | 网格管道宽度补偿系数。四边管道的有效宽度在 `curveWidth` 和 `curveWidth/sqrt(2)` 之间变化，乘以 1.11 使平均宽度匹配目标值 |

## 依赖关系

### 本文件引用

**头文件 (`CurveTessellation.h`)**:
- `Core/Macros.h`：`FALCOR_API` 导出宏
- `Utils/Math/Matrix.h`：`float4x4` 矩阵类型
- `Utils/Math/Vector.h`：`float2`、`float3` 向量类型
- `Utils/fast_vector.h`：高性能动态数组
- `<vector>`：标准向量容器

**实现文件 (`CurveTessellation.cpp`)**:
- `Core/Error.h`：`FALCOR_ASSERT` 断言宏
- `Utils/Math/Common.h`：`div_round_up` 等数学工具
- `Utils/Math/MathHelpers.h`：`buildFrame` 正交坐标系构建
- `Utils/Math/CubicSpline.h`：三次样条插值模板类
- `Utils/Math/Quaternion.h`：四元数旋转（`quatFromRotationBetweenVectors`）

### 被以下文件引用

- 场景加载器：在导入曲线资产时调用细分方法
- `CurveConfig.h`：通过枚举类型选择细分模式

## 实现细节

### 线性扫掠球转换流程

1. 预估输出大小并预分配内存
2. 遍历每条曲线链（按 `keepOneEveryXStrands` 抽稀）
3. 对每条链调用 `optimizeStrandGeometry` 移除重复点并进行样条插值细分
4. 对细分后的点进行样条插值，生成球心位置和半径
5. 通过 `transformSphere` 应用预变换矩阵
6. 半径经过 `sanitizeWidth` 确保非零

### 多边形管道转换流程

1. 预估输出大小（顶点数 = 横截面点数 x 细分点数，面数 = 2 x 横截面点数 x 段数）
2. 遍历每条曲线链，调用 `optimizeStrandGeometry` 优化
3. 构建初始正交坐标系（`buildFrame`）
4. 沿曲线逐点更新坐标系（`updateCurveFrame`，使用四元数最小旋转）
5. 在每个横截面生成环形分布的顶点（`updateMeshResultBuffers`）
6. 连接相邻横截面生成三角面片（`connectFaceVertices`）

### 关键算法

- **三次样条插值**: 使用 `CubicSpline` 模板类对控制点、宽度、UV 分别进行插值，保证曲线的平滑性
- **最小扭转帧**: 通过四元数旋转（`quatFromRotationBetweenVectors`）在相邻切线之间平滑过渡，避免帧突变
- **几何优化**: 先移除重复控制点，再进行细分和抽稀，减少不必要的计算
