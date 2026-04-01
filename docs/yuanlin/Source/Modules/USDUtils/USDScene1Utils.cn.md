# USDScene1Utils 源码文档

> 路径: `Source/Modules/USDUtils/USDScene1Utils.h`, `Source/Modules/USDUtils/USDScene1Utils.cpp`
> 类型: C++ 头文件 + 实现
> 模块: USDUtils

## 功能概述

提供与 `SceneBuilder` 集成的 USD 场景导入辅助函数，包括场景节点创建、属性频率计算和插值模式转换。`.cpp` 文件仅包含头文件引用。

## 工具函数

| 函数签名 | 说明 |
|----------|------|
| `SceneBuilder::Node makeNode(const std::string& name, NodeID parentId)` | 创建一个单位变换的场景节点 |
| `SceneBuilder::Node makeNode(const std::string& name, const float4x4& xform, const float4x4& bindTransform, NodeID parentId)` | 创建带指定变换和绑定变换的场景节点 |
| `size_t computeElementCount(AttributeFrequency freq, size_t faceCount, size_t vertexCount)` | 根据属性频率计算元素数量（常量=1，uniform=面数，vertex=顶点数，faceVarying=3*面数） |
| `size_t computePerFaceElementCount(AttributeFrequency freq, size_t faceCount)` | 计算每面属性元素数量（uniform 或 faceVarying） |
| `AttributeFrequency convertInterpolation(const TfToken& mode)` | 将 USD 插值模式（constant/uniform/vertex/varying/faceVarying）转换为 Falcor 的 `AttributeFrequency` |

## 依赖关系

### 本文件引用

- `USDHelpers.h` - 警告宏
- `USDUtils.h` - 核心工具函数
- `Scene/SceneBuilder.h` - `SceneBuilder::Node`、`AttributeFrequency`
- USD 几何体 API（`UsdGeomMesh`、`UsdGeomBasisCurves` 等）

### 被以下文件引用

- `USDImporter/ImporterContext.h` - 导入器上下文

## 实现细节

- `vertex` 和 `varying` 插值模式在 Falcor 中被视为同义词，均映射为 `AttributeFrequency::Vertex`。
- 节点创建使用单位变换作为默认的局部动画矩阵。
