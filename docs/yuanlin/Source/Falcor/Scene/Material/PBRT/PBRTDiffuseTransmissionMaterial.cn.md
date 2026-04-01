# PBRTDiffuseTransmissionMaterial 源码文档

> 路径: `Source/Falcor/Scene/Material/PBRT/PBRTDiffuseTransmissionMaterial.h` + `.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Material/PBRT

## 功能概述

实现了朗伯漫反射透射材质（Diffuse Transmission）。材质同时具有反射和透射波瓣，两者均不依赖于出射方向 wo。反射波瓣的反照率由 BaseColor 指定，透射波瓣的反照率由 Transmission 指定。该类完美匹配 PBRT 中的 "diffusetransmission" 材质。

### 纹理通道布局

| 通道 | 内容 |
|------|------|
| BaseColor RGB | 基础颜色（反射反照率） |
| BaseColor A | 不透明度 |
| Transmission RGB | 透射颜色 |
| Normal | 3通道标准法线贴图或 2通道 BC5 格式 |

## 类与接口

### `PBRTDiffuseTransmissionMaterial`

- **继承**: `BasicMaterial`
- **职责**: 管理漫反射透射材质参数和着色器加载

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<PBRTDiffuseTransmissionMaterial> create(ref<Device>, const std::string&)` | 工厂方法 |
| `ProgramDesc::ShaderModuleList getShaderModules() const override` | 返回着色器模块 |
| `TypeConformanceList getTypeConformances() const override` | 注册为 `IMaterial` |

## 依赖关系

### 本文件引用

- `Scene/Material/BasicMaterial.h` — 基类
- `Utils/Scripting/ScriptBindings.h` — Python 绑定
- `GlobalState.h`

### 被以下文件引用

- 材质系统（通过 `MaterialType::PBRTDiffuseTransmission`）
- `Rendering/Materials/PBRT/PBRTDiffuseTransmissionMaterial.slang`

## 实现细节

- BaseColor 和 Transmission 纹理槽均标记为 `sRGB = true`
- 类接口最为简洁，没有额外的粗糙度参数或序列化支持
- 该材质无粗糙度参数，是纯朗伯体模型
