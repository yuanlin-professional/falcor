# AssimpImporter - Assimp 场景导入器 (支持30+格式)

## 功能概述

AssimpImporter 是 Falcor 的通用场景导入插件，基于开源库 [Assimp (Open Asset Import Library)](https://github.com/assimp/assimp) 实现，支持超过 30 种三维模型和场景文件格式的导入。该插件继承自 `Falcor::Importer` 基类，通过 Falcor 插件系统注册，可在运行时被自动发现和加载。

### 支持的文件格式

`fbx`, `gltf`, `glb`, `obj`, `dae`, `x`, `md5mesh`, `ply`, `3ds`, `blend`, `ase`, `ifc`, `xgl`, `zgl`, `dxf`, `lwo`, `lws`, `lxo`, `stl`, `ac`, `ms3d`, `cob`, `scn`, `3d`, `mdl`, `mdl2`, `pk3`, `smd`, `vta`, `raw`, `ter`

### 主要功能

- **文件导入** (`importScene`): 从文件路径加载场景，解析网格、材质、灯光、相机、骨骼动画等数据，并通过 `SceneBuilder` 构建 Falcor 场景。
- **内存导入** (`importSceneFromMemory`): 从内存缓冲区导入场景数据，支持同样的格式集合。
- **材质转换**: 将 Assimp 材质参数（漫反射、高光、法线贴图、金属度、粗糙度等）映射到 Falcor 的 `StandardMaterial` 材质系统。
- **骨骼动画支持**: 解析骨骼层级和关键帧动画，构建 Falcor 的骨骼动画系统。
- **网格处理**: 支持顶点位置、法线、切线、纹理坐标、骨骼权重等属性的导入。
- **场景图遍历**: 递归遍历 Assimp 节点树，正确处理变换继承关系。

## 文件清单

| 文件名 | 类型 | 说明 |
|--------|------|------|
| `AssimpImporter.h` | 头文件 | 声明 `AssimpImporter` 类，继承自 `Importer`，注册插件元信息及支持的文件扩展名 |
| `AssimpImporter.cpp` | 源文件 | 实现场景导入的核心逻辑，包括网格、材质、灯光、相机、骨骼动画的解析和转换 |
| `CMakeLists.txt` | 构建文件 | CMake 构建配置，链接 assimp 外部库 |

## 依赖关系

### 外部依赖

- **assimp**: Open Asset Import Library，核心的三维模型解析库，通过 CMake `target_link_libraries` 链接。

### Falcor 内部依赖

- `Scene/Importer.h` - 导入器基类接口
- `Scene/SceneBuilder.h` - 场景构建器，用于组装最终场景
- `Scene/Material/StandardMaterial.h` - 标准材质类型
- `Scene/Animation/Animation.h` - 动画系统
- `Core/Error.h` - 错误处理
- `Utils/Logger.h` - 日志系统
- `Utils/Math/` - 数学工具（矩阵、向量变换）
