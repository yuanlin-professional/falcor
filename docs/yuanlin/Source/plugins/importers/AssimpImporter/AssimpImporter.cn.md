# AssimpImporter 源码文档

> 路径: `Source/plugins/importers/AssimpImporter/AssimpImporter.h`, `AssimpImporter.cpp`
> 类型: C++ 头文件 + 实现
> 模块: plugins/importers/AssimpImporter

## 功能概述

基于 Assimp（Open Asset Import Library）的场景导入器插件，支持超过 30 种 3D 文件格式（FBX、glTF、OBJ、DAE、PLY、3DS、Blend 等）。将 Assimp 加载的场景数据（网格、材质、纹理、骨骼动画、相机、灯光）转换为 Falcor 的 `SceneBuilder` 表示。同时支持从文件路径和内存缓冲区导入。

## 类与接口

### `AssimpImporter`

- **继承**: `Importer`
- **插件注册**: `FALCOR_PLUGIN_CLASS`，插件名 `"AssimpImporter"`
- **支持格式**: fbx, gltf, obj, dae, x, md5mesh, ply, 3ds, blend, ase, ifc, xgl, zgl, dxf, lwo, lws, lxo, stl, ac, ms3d, cob, scn, 3d, mdl, mdl2, pk3, smd, vta, raw, ter, glb

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static std::unique_ptr<Importer> create()` | 工厂方法 |
| `void importScene(const path&, SceneBuilder&, const map&)` | 从文件路径导入场景 |
| `void importSceneFromMemory(const void*, size_t, string_view, SceneBuilder&, const map&)` | 从内存缓冲区导入场景 |

## 依赖关系

### 本文件引用

- `Scene/Importer.h` - 导入器基类
- `Scene/SceneBuilder.h` - 场景构建器
- Assimp 库（通过 `.cpp` 中的 `assimp/Importer.hpp` 等）
- Falcor 核心 API（`Texture`、`Material`、`Camera`、`Light`、`Animation` 等）

## 实现细节

- 使用 Assimp 的后处理标志进行三角化、切线计算、冗余材质移除等。
- 递归遍历 Assimp 场景节点树，构建 Falcor 节点层级。
- 材质转换支持 PBR 工作流（金属度/粗糙度）和传统工作流。
- 纹理路径解析支持嵌入纹理和外部文件引用。
- 骨骼动画通过关键帧转换导入，支持平移、旋转、缩放通道。
- glTF 的 KHR_materials 扩展通过 Assimp 的 metadata 系统处理。
