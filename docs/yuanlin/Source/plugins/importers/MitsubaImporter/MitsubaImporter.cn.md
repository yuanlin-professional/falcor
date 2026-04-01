# MitsubaImporter 源码文档

> 路径: `Source/plugins/importers/MitsubaImporter/MitsubaImporter.h`, `MitsubaImporter.cpp`
> 类型: C++ 头文件 + 实现
> 模块: plugins/importers/MitsubaImporter

## 功能概述

Mitsuba 渲染器场景文件（`.xml`）的导入器插件。解析 Mitsuba XML 场景描述，将其中的形状（shape）、材质（BSDF）、光源（emitter）、相机（sensor）和介质（medium）转换为 Falcor 的 `SceneBuilder` 表示。支持 Mitsuba v1 到 v2 的格式升级。

## 类与接口

### `MitsubaImporter`

- **继承**: `Importer`
- **插件注册**: `FALCOR_PLUGIN_CLASS`，插件名 `"MitsubaImporter"`
- **支持格式**: xml

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static std::unique_ptr<Importer> create()` | 工厂方法 |
| `void importScene(const path&, SceneBuilder&, const map&)` | 解析 XML 文件并构建场景 |

### 内部结构体（`Mitsuba` 命名空间）

| 结构体 | 说明 |
|--------|------|
| `BuilderContext` | 场景构建上下文，持有 `SceneBuilder` 引用和实例映射 |
| `ShapeInfo` | 形状信息：网格、变换、材质 |
| `SensorInfo` | 传感器信息：相机、变换 |
| `EmitterInfo` | 发射器信息：环境贴图或灯光、变换 |
| `TextureInfo` | 纹理信息：值或纹理对象、UV 变换 |
| `BSDFInfo` | BSDF 信息：材质引用 |
| `MediumInfo` | 介质信息：均匀介质的散射/吸收系数 |

### 支持的 BSDF 类型

| 类型 | 转换目标 |
|------|---------|
| `diffuse` | `PBRTDiffuseMaterial` |
| `dielectric` / `roughdielectric` | `StandardMaterial`（镜面透射） |
| `thindielectric` | `StandardMaterial`（薄表面） |
| `conductor` / `roughconductor` | `PBRTConductorMaterial` |
| `plastic` / `roughplastic` | `StandardMaterial` |
| `twosided` | 内部材质 + 双面标记 |

### 支持的形状类型

| 类型 | 说明 |
|------|------|
| `obj` / `ply` | 从文件加载三角网格 |
| `sphere` / `disk` / `rectangle` / `cube` | 生成基本几何体 |

## 依赖关系

### 本文件引用

- `Parser.h` - XML 解析器
- `Tables.h` - IOR 查找表
- `Scene/Material/PBRT/PBRTDiffuseMaterial.h` 等 - PBRT 材质类型
- `pugixml` - XML 解析库
- `pybind11` - Python 绑定

## 实现细节

- XML 解析使用 `pugixml`，解析后构建实例映射表，再遍历构建场景。
- 支持 v1 → v2 自动升级：属性名驼峰转下划线、旧纹理参数转 `to_uv` 变换块等。
- Mitsuba 的 Z-up 坐标系通过变换矩阵转换为 Falcor 的 Y-up。
- 面积发射器（area emitter）必须嵌套在形状内，转换为 `StandardMaterial` 的自发光属性。
- 菲涅尔导体反射率使用精确的 Fresnel-conductor 公式计算。
