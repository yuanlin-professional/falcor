# PBRTDiffuseMaterial 源码文档

> 路径: `Source/Falcor/Scene/Material/PBRT/PBRTDiffuseMaterial.h` + `.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Material/PBRT

## 功能概述

实现了朗伯漫反射材质（Lambertian Diffuse），反射率不依赖于出射方向 wo。该类完美匹配 PBRT 中的 "diffuse" 材质。

### 纹理通道布局

| 通道 | 内容 |
|------|------|
| BaseColor RGB | 基础颜色 |
| BaseColor A | 不透明度 |
| Normal | 3通道标准法线贴图或 2通道 BC5 格式 |

## 类与接口

### `PBRTDiffuseMaterial`

- **继承**: `BasicMaterial`
- **职责**: 管理朗伯漫反射材质参数、着色器加载和参数序列化

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<PBRTDiffuseMaterial> create(ref<Device>, const std::string&)` | 工厂方法 |
| `ProgramDesc::ShaderModuleList getShaderModules() const override` | 返回着色器模块 |
| `TypeConformanceList getTypeConformances() const override` | 注册为 `IMaterial` |
| `const MaterialParamLayout& getParamLayout() const override` | 返回参数布局 |
| `SerializedMaterialParams serializeParams() const override` | 序列化材质参数 |
| `void deserializeParams(const SerializedMaterialParams&) override` | 反序列化材质参数 |

## 依赖关系

### 本文件引用

- `Scene/Material/BasicMaterial.h` — 基类
- `PBRTDiffuseMaterialParamLayout.slang` — 参数布局
- `Utils/Scripting/ScriptBindings.h` — Python 绑定
- `GlobalState.h`

### 被以下文件引用

- 材质系统（通过 `MaterialType::PBRTDiffuse`）
- `Rendering/Materials/PBRT/PBRTDiffuseMaterial.slang`

## 实现细节

- BaseColor 纹理槽标记为 `sRGB = true`
- 仅配置了 BaseColor 和 Normal 两个纹理槽
- 支持参数序列化，通过 `PBRTDiffuseMaterialParamLayout` 实现
- Python 绑定提供两种构造方式：无参（使用活跃 SceneBuilder 设备）和显式设备参数
