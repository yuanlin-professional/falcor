# PBRTDielectricMaterial 源码文档

> 路径: `Source/Falcor/Scene/Material/PBRT/PBRTDielectricMaterial.h` + `.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Material/PBRT

## 功能概述

实现了电介质材质（Dielectric）。表面同时具有（无色的）反射和折射波瓣。材质可以表现为理想光滑表面（粗糙度为 0 时）或（可能各向异性的）GGX 微面元。该类完美匹配 PBRT 中的 "dielectric" 材质。

### 纹理通道布局

| 通道 | 内容 |
|------|------|
| Specular R | X 方向粗糙度 |
| Specular G | Y 方向粗糙度 |
| Normal | 3通道标准法线贴图或 2通道 BC5 格式 |

## 类与接口

### `PBRTDielectricMaterial`

- **继承**: `BasicMaterial`
- **职责**: 管理电介质材质参数、着色器加载和 UI 渲染

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<PBRTDielectricMaterial> create(ref<Device>, const std::string&)` | 工厂方法 |
| `ProgramDesc::ShaderModuleList getShaderModules() const override` | 返回着色器模块 |
| `TypeConformanceList getTypeConformances() const override` | 注册为 `IMaterial` |
| `void setRoughness(float2 roughness)` | 设置 X/Y 粗糙度 |
| `float2 getRoughness() const` | 获取粗糙度 |
| `void renderSpecularUI(Gui::Widgets&) override` | 渲染高光参数 UI |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mData.specular[0..1]` | `float16_t` | X/Y 粗糙度 |

## 依赖关系

### 本文件引用

- `Scene/Material/BasicMaterial.h` — 基类
- `Utils/Scripting/ScriptBindings.h` — Python 绑定
- `GlobalState.h`

### 被以下文件引用

- 材质系统（通过 `MaterialType::PBRTDielectric`）
- `Rendering/Materials/PBRT/PBRTDielectricMaterial.slang`

## 实现细节

- 仅配置了 Specular（红+绿通道）和 Normal 纹理槽，不使用 BaseColor 和 Transmission
- 电介质材质的反射/折射颜色由折射率决定，不需要额外的颜色纹理
- 提供 Python 绑定，暴露 `roughness` 属性
