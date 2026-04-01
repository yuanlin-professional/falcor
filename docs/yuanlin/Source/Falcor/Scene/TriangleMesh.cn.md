# TriangleMesh 源码文档

> 路径: `Source/Falcor/Scene/TriangleMesh.h` + `Source/Falcor/Scene/TriangleMesh.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene

## 功能概述

TriangleMesh 是一个简单的索引三角形网格工具类，顶点包含位置、法线和纹理坐标属性。它主要用于程序化创建简单几何体（四边形、立方体、球体、圆盘等）或从文件加载网格，然后传递给 SceneBuilder。

## 类与接口

### `TriangleMesh`

- **继承**: `Object`（Falcor 对象基类）
- **职责**: 创建和管理简单的三角形网格数据

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<TriangleMesh> create()` | 创建空网格 |
| `static ref<TriangleMesh> create(const VertexList&, const IndexList&, bool frontFaceCW)` | 从顶点和索引数据创建网格 |
| `static ref<TriangleMesh> createDummy()` | 创建虚拟网格（单个退化三角形） |
| `static ref<TriangleMesh> createQuad(float2 size)` | 创建四边形（Y+ 法线） |
| `static ref<TriangleMesh> createDisk(float radius, uint32_t segments)` | 创建圆盘 |
| `static ref<TriangleMesh> createCube(float3 size)` | 创建立方体 |
| `static ref<TriangleMesh> createSphere(float radius, uint32_t segmentsU, uint32_t segmentsV)` | 创建 UV 球体 |
| `static ref<TriangleMesh> createFromFile(const path&, ImportFlags)` | 从文件加载网格（使用 ASSIMP） |
| `uint32_t addVertex(float3 position, float3 normal, float2 texCoord)` | 添加顶点 |
| `void addTriangle(uint32_t i0, uint32_t i1, uint32_t i2)` | 添加三角形 |
| `void applyTransform(const Transform&)` | 应用变换到网格 |
| `void applyTransform(const float4x4&)` | 应用矩阵变换到网格 |

#### `Vertex` 结构体

| 成员 | 类型 | 说明 |
|------|------|------|
| `position` | `float3` | 顶点位置 |
| `normal` | `float3` | 顶点法线 |
| `texCoord` | `float2` | 纹理坐标 |

#### `ImportFlags` 枚举

| 标志 | 说明 |
|------|------|
| `GenSmoothNormals` | 生成平滑法线 |
| `JoinIdenticalVertices` | 合并相同顶点 |

## 依赖关系

### 本文件引用

- `Transform.h` - 变换类
- `Core/Object.h` - 对象基类
- `Utils/Math/Vector.h`, `Matrix.h` - 数学类型

### 被以下文件引用

- `SceneBuilder.h` - 作为简单几何体传递给场景构建器
- 应用程序代码中程序化创建几何体

## 实现细节

- `createFromFile()` 使用 ASSIMP 库支持多种资产格式，将所有几何体预变换后合并到单个网格中
- 工厂方法创建的几何体均居中于原点
