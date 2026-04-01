# SceneCache 源码文档

> 路径: `Source/Falcor/Scene/SceneCache.h` + `Source/Falcor/Scene/SceneCache.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene

## 功能概述

SceneCache 提供场景缓存的读写功能，通过将 `Scene::SceneData` 的二进制表示存储到磁盘，大幅减少复杂资产的加载时间。缓存以 SHA1 哈希作为键值，确保资产变化后自动失效。

## 类与接口

### `SceneCache`

- **继承**: 无
- **职责**: 管理场景缓存文件的读写，序列化/反序列化所有场景数据

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static bool hasValidCache(const Key& key)` | 检查指定键是否存在有效缓存 |
| `static void writeCache(const Scene::SceneData&, const Key&)` | 将场景数据写入缓存文件 |
| `static Scene::SceneData readCache(ref<Device>, const Key&)` | 从缓存文件读取场景数据 |

#### 内部序列化方法

| 方法 | 说明 |
|------|------|
| `writeCamera / readCamera` | 序列化/反序列化相机 |
| `writeLight / readLight` | 序列化/反序列化光源 |
| `writeMaterials / readMaterials` | 序列化/反序列化材质系统 |
| `writeGridVolume / readGridVolume` | 序列化/反序列化网格体积 |
| `writeGrid / readGrid` | 序列化/反序列化网格 |
| `writeEnvMap / readEnvMap` | 序列化/反序列化环境贴图 |
| `writeTransform / readTransform` | 序列化/反序列化变换 |
| `writeAnimation / readAnimation` | 序列化/反序列化动画 |
| `writeSplitBuffer / readSplitBuffer` | 序列化/反序列化分割缓冲区 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `Key` | `SHA1::MD` | 缓存键类型（SHA1 消息摘要） |

## 依赖关系

### 本文件引用

- `Scene.h` - Scene 及其 SceneData 类型
- `Animation/Animation.h` - 动画数据
- `Camera/Camera.h` - 相机数据
- `Lights/EnvMap.h`, `Light.h` - 光源数据
- `Volume/Grid.h`, `GridVolume.h` - 体积数据
- `Material/BasicMaterial.h`, `MaterialSystem.h` - 材质数据
- `Utils/CryptoUtils.h` - SHA1 哈希

### 被以下文件引用

- `SceneBuilder` - 在构建场景时检查/使用缓存

## 实现细节

- 使用内部的 `OutputStream` 和 `InputStream` 类进行二进制流操作
- 通过 `writeMarker` / `readMarker` 在数据流中插入标记，用于验证数据完整性
- 缓存路径由 `getCachePath(key)` 根据哈希值确定
