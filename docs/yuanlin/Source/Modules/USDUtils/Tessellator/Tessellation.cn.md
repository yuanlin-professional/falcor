# Tessellation 源码文档

> 路径: `Source/Modules/USDUtils/Tessellator/Tessellation.h`, `Source/Modules/USDUtils/Tessellator/Tessellation.cpp`
> 类型: C++ 头文件 + 实现
> 模块: USDUtils / Tessellator

## 功能概述

提供 USD 网格的细分与三角化功能。支持无细分的简单三角化（扇形分割）和基于 OpenSubdiv 的细分曲面评估（Catmull-Clark、Loop、Bilinear 方案）。处理法线生成、纹理坐标插值、孔面跳过等。

## 类与接口

### `MeshTopology`

- **职责**: 存储 USD 网格拓扑信息。

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `scheme` | `TfToken` | 细分方案（"none"、"catmullClark"、"loop"、"bilinear"） |
| `orient` | `TfToken` | 面朝向（"leftHanded" 或 "rightHanded"） |
| `faceCounts` | `VtIntArray` | 每面的顶点数 |
| `faceIndices` | `VtIntArray` | 面-顶点索引 |
| `holeIndices` | `VtIntArray` | 孔面索引（已排序） |

### `UsdMeshData`

- **职责**: 使用 USD 数据类型表示的基础网格数据。

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `topology` | `MeshTopology` | 拓扑数据 |
| `points` | `VtVec3fArray` | 顶点位置 |
| `normals` | `VtVec3fArray` | 着色法线 |
| `uvs` | `VtVec2fArray` | 纹理坐标 |
| `normalInterp` | `TfToken` | 法线插值模式 |
| `uvInterp` | `TfToken` | 纹理坐标插值模式 |

### `MeshIndexer`（内部类）

- **职责**: 将逐面的小面片数据聚合为索引网格，共享相同位置的顶点。

## 函数

| 函数签名 | 说明 |
|----------|------|
| `UsdMeshData tessellate(const UsdGeomMesh&, const UsdMeshData&, uint32_t maxRefinementLevel, VtIntArray& coarseFaceIndices)` | 主入口。根据细分方案和精化等级选择三角化或细分路径 |

## 依赖关系

### 本文件引用

- `Core/Error.h` - 断言
- `Utils/Logger.h` - 日志
- `Utils/Math/FNVHash.h` - FNV 哈希
- `UsdIndexedVector.h` - 去重索引容器
- `USDUtils/USDUtils.h`、`USDHelpers.h` - 工具函数
- OpenSubdiv: `Far`（拓扑）、`Bfr`（曲面评估和细分）、`Sdc`（方案选项）

### 被以下文件引用

- `USDImporter/ImporterContext.cpp` - 网格处理流程

## 实现细节

- **简单三角化**（`triangulate`）: 使用扇形分割（fan triangulation），保留顶点顺序以兼容骨骼索引等。左手/右手朝向通过交换索引顺序处理。无法线时生成 uniform（平面）法线。
- **OpenSubdiv 细分**: 使用 `Bfr::RefinerSurfaceFactory` 和 `Bfr::Tessellation` API。面变化（face-varying）UV 需构建索引后传入 OpenSubdiv。精化后法线始终为逐顶点生成（根据 USD 规范）。
- Loop 方案仅支持全三角形输入网格。
