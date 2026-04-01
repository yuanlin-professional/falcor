# PBRTCoatedConductorMaterial 源码文档

> 路径: `Source/Falcor/Scene/Material/PBRT/PBRTCoatedConductorMaterial.h` + `.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Material/PBRT

## 功能概述

实现了带涂层的导体材质（Coated Conductor），即在导体基底上方覆盖一层电介质涂层。导体和涂层均可以是光滑或粗糙的、各向同性或各向异性的。材质通过蒙特卡洛随机游走模拟层间的互反射。该类完美匹配 PBRT 中的 "coatedconductor" 材质。

### 纹理通道布局

| 通道 | 内容 |
|------|------|
| BaseColor RGB | 导体复数折射率 Eta |
| BaseColor A | 不透明度 |
| Transmission RGB | 导体复数 k |
| Specular R | 界面 X 方向粗糙度 |
| Specular G | 界面 Y 方向粗糙度 |
| Specular B | 导体 X 方向粗糙度 |
| Specular A | 导体 Y 方向粗糙度 |
| Normal | 3通道标准法线贴图或 2通道 BC5 格式 |

## 类与接口

### `PBRTCoatedConductorMaterial`

- **继承**: `BasicMaterial`
- **职责**: 管理带涂层导体材质的参数设置、着色器模块加载和 UI 渲染

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<PBRTCoatedConductorMaterial> create(ref<Device>, const std::string&)` | 工厂方法，创建材质实例 |
| `ProgramDesc::ShaderModuleList getShaderModules() const override` | 返回着色器模块列表，指向 `Rendering/Materials/PBRT/PBRTCoatedConductorMaterial.slang` |
| `TypeConformanceList getTypeConformances() const override` | 返回类型一致性列表，注册为 `IMaterial` |
| `void setRoughness(float4 roughness)` | 设置四通道粗糙度（界面XY + 导体XY） |
| `float4 getRoughness() const` | 获取四通道粗糙度 |
| `void renderSpecularUI(Gui::Widgets&) override` | 渲染高光参数的 UI 控件 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mData.specular[0..3]` | `float16_t` | 存储四个粗糙度分量（继承自 BasicMaterial） |

## 依赖关系

### 本文件引用

- `Scene/Material/BasicMaterial.h` — 基类
- `Utils/Scripting/ScriptBindings.h` — Python 脚本绑定
- `GlobalState.h` — 全局状态访问

### 被以下文件引用

- 材质系统注册表（通过 `MaterialType::PBRTCoatedConductor`）
- `Rendering/Materials/PBRT/PBRTCoatedConductorMaterial.slang` — GPU 端着色器实现

## 实现细节

- 构造函数中配置了四个纹理槽：BaseColor（RGBA）、Transmission（RGB）、Specular（RGBA）、Normal（RGB）
- 粗糙度使用 `float16_t` 精度存储在 `mData.specular` 数组中
- 修改粗糙度时通过 `markUpdates(UpdateFlags::DataChanged)` 标记数据变更
- 提供 Python 绑定，暴露 `roughness` 属性
