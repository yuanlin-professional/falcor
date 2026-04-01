# PBRTImporter 源码文档

> 路径: `Source/plugins/importers/PBRTImporter/PBRTImporter.h`, `PBRTImporter.cpp`
> 类型: C++ 头文件 + 实现
> 模块: plugins/importers/PBRTImporter

## 功能概述

pbrt-v4 场景文件（`.pbrt`）的导入器插件。解析 pbrt 场景描述语言，将材质、纹理、形状、灯光、相机和介质转换为 Falcor 的 `SceneBuilder` 表示。基于 pbrt 项目的代码（Apache-2.0 许可）。

## 类与接口

### `PBRTImporter`

- **继承**: `Importer`
- **插件注册**: `FALCOR_PLUGIN_CLASS`，插件名 `"PBRTImporter"`
- **支持格式**: pbrt

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static std::unique_ptr<Importer> create()` | 工厂方法 |
| `void importScene(const path&, SceneBuilder&, const map&)` | 解析 pbrt 文件并构建 Falcor 场景 |

## 依赖关系

### 本文件引用

- `Scene/Importer.h` - 导入器基类
- `Builder.h` - `BasicScene`、`BasicSceneBuilder`
- `Parser.h` - pbrt 词法/语法解析器
- 多个 Falcor 材质类（`StandardMaterial`、`PBRTDiffuseMaterial`、`PBRTConductorMaterial` 等）
- `EnvMapConverter.h` - 环境贴图格式转换

## 实现细节

- 导入流程分两阶段：1) 解析 pbrt 文件构建 `BasicScene` 中间表示；2) 将 `BasicScene` 转换为 Falcor 的 `SceneBuilder`。
- 支持的材质类型：diffuse、coateddiffuse、conductor、dielectric、diffusetransmission、hair、interface、measured、mix、subsurface、thindielectric。
- 支持的形状类型：trianglemesh、plymesh、sphere、disk、cylinder、bilinearmesh、curve、loopsubdiv。
- 支持的灯光类型：point、spot、distant、projection、infinite、goniometric、area（diffuse/uniform）。
- 相机类型：perspective、orthographic（realistic 不支持）。
- pbrt 使用左手坐标系行向量矩阵，导入时转置。
- 环境贴图支持等面积八面体映射到经纬度映射的转换。
