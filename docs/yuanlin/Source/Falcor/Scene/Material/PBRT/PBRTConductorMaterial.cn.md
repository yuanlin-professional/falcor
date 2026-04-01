# PBRTConductorMaterial 源码文档

> 路径: `Source/Falcor/Scene/Material/PBRT/PBRTConductorMaterial.h` + `.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Material/PBRT

## 功能概述

实现了导体材质（Conductor）。表面为纯反射性质，反照率由导体菲涅耳方程（即复数折射率 IoR）决定。材质可以表现为镜面（粗糙度为 0 时）或（可能各向异性的）GGX 微面元。该类完美匹配 PBRT 中的 "conductor" 材质。

### 纹理通道布局

| 通道 | 内容 |
|------|------|
| BaseColor RGB | 复数折射率 Eta |
| BaseColor A | 不透明度 |
| Transmission RGB | 复数 k（消光系数） |
| Specular R | X 方向粗糙度 |
| Specular G | Y 方向粗糙度 |
| Normal | 3通道标准法线贴图或 2通道 BC5 格式 |

## 类与接口

### `PBRTConductorMaterial`

- **继承**: `BasicMaterial`
- **职责**: 管理导体材质参数、着色器加载、参数序列化和 UI 渲染

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<PBRTConductorMaterial> create(ref<Device>, const std::string&)` | 工厂方法 |
| `ProgramDesc::ShaderModuleList getShaderModules() const override` | 返回着色器模块 |
| `TypeConformanceList getTypeConformances() const override` | 注册为 `IMaterial` |
| `void setRoughness(float2 roughness)` | 设置 X/Y 粗糙度 |
| `float2 getRoughness() const` | 获取粗糙度 |
| `const MaterialParamLayout& getParamLayout() const override` | 返回材质参数布局 |
| `SerializedMaterialParams serializeParams() const override` | 序列化材质参数 |
| `void deserializeParams(const SerializedMaterialParams&) override` | 反序列化材质参数 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mData.specular[0..1]` | `float16_t` | X/Y 粗糙度 |

## 依赖关系

### 本文件引用

- `Scene/Material/BasicMaterial.h` — 基类
- `PBRTConductorMaterialParamLayout.slang` — 参数布局定义
- `Utils/Scripting/ScriptBindings.h` — Python 绑定
- `GlobalState.h`

### 被以下文件引用

- 材质系统（通过 `MaterialType::PBRTConductor`）
- `Rendering/Materials/PBRT/PBRTConductorMaterial.slang`

## 实现细节

- 支持材质参数的序列化/反序列化，通过 `PBRTConductorMaterialParamLayout` 实现
- 参数布局定义了 `eta`（float3）、`k`（float3）和 `roughness`（float2）三组参数
- 最小粗糙度限制为 0.05（定义在 ParamLayout 中的 `kPBRTConductorMinRoughness`）
