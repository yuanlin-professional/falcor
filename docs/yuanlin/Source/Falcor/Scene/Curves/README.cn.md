# Scene/Curves -- 曲线几何 (Curve Geometry)

## 功能概述

Curves 模块负责将输入的曲线数据（如头发、毛皮等参数化曲线）转换为可供渲染管线使用的几何表示。核心功能包括：

- **线性扫掠球 (Linear Swept Sphere)**：将三次 B 样条曲线细分为线性扫掠球段，适用于光线追踪硬件加速的曲线交叉测试。支持可配置的细分密度、稀疏化抽样和预变换。
- **多面管 (PolyTube)**：将三次 B 样条曲线细化为三角网格管状体，生成顶点、法线、切线和纹理坐标，适用于光栅化渲染路径。
- **配置枚举**：通过 `CurveTessellationMode` 枚举在两种细化模式间切换。

## 文件清单

| 文件名 | 类型 | 说明 |
|--------|------|------|
| `CurveConfig.h` | C++ Header | 定义 `CurveTessellationMode` 枚举（`LinearSweptSphere` / `PolyTube`） |
| `CurveTessellation.h` | C++ Header | `CurveTessellation` 类声明，包含 `SweptSphereResult` 与 `MeshResult` 结果结构体 |
| `CurveTessellation.cpp` | C++ Source | 曲线细化的完整实现，包括 B 样条插值、几何优化与网格构建 |

## 依赖关系

### 内部依赖
- `Core/Macros`, `Core/Error` -- 宏定义与错误处理
- `Utils/Math/Matrix`, `Utils/Math/Vector` -- 矩阵/向量数学
- `Utils/Math/CubicSpline` -- 三次样条插值
- `Utils/Math/Quaternion` -- 四元数旋转（用于曲线坐标系平滑旋转）
- `Utils/Math/MathHelpers`, `Utils/Math/Common` -- 通用数学工具
- `Utils/fast_vector` -- 高性能动态数组

### 外部依赖
- 无第三方外部依赖

## 关键类与接口

### `CurveTessellationMode` (枚举)
```cpp
enum class CurveTessellationMode {
    LinearSweptSphere,  // 光线追踪友好的扫掠球表示
    PolyTube,           // 光栅化友好的三角网格管
};
```

### `CurveTessellation` (静态工具类)

该类不可实例化，所有方法均为静态。

#### `SweptSphereResult` 结构体
光线追踪曲线表示的输出结果：
- `degree` -- 多项式阶数（当前仅支持线性 degree=1）
- `indices` -- 段索引
- `points` / `radius` -- 控制点坐标与半径
- `texCrds` -- 纹理坐标

#### `MeshResult` 结构体
光栅化管状网格的输出结果：
- `vertices` / `normals` / `tangents` -- 顶点属性
- `radii` -- 每顶点曲线半径
- `texCrds` -- 纹理坐标
- `faceVertexCounts` / `faceVertexIndices` -- 面片拓扑

#### 核心方法

| 方法 | 说明 |
|------|------|
| `convertToLinearSweptSphere(...)` | 将三次 B 样条转换为线性扫掠球段。参数包括线段数、细分密度、稀疏比例、宽度缩放、预变换矩阵等 |
| `convertToPolytube(...)` | 将三次 B 样条细化为三角网格管。额外参数 `pointCountPerCrossSection` 控制截面多边形的精度 |

### 内部实现细节
- 使用 `CubicSpline<T>` 对控制点、宽度、UV 进行样条插值
- 通过四元数实现相邻段之间坐标系的平滑旋转（避免扭曲）
- 自动去除重复控制点以优化几何体
- 半径保证不为零（用于区分曲线网格与原生网格）
