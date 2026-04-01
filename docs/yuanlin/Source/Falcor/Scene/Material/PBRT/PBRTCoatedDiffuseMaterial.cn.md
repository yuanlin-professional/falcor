# PBRTCoatedDiffuseMaterial 源码文档

> 路径: `Source/Falcor/Scene/Material/PBRT/PBRTCoatedDiffuseMaterial.h` + `.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Material/PBRT

## 功能概述

实现了带涂层的漫反射材质（Coated Diffuse），即在朗伯体基底上方覆盖一层电介质涂层。涂层可以是光滑或粗糙的、各向同性或各向异性的。材质通过蒙特卡洛随机游走模拟层间的互反射。该类完美匹配 PBRT 中的 "coateddiffuse" 材质。

### 纹理通道布局

| 通道 | 内容 |
|------|------|
| BaseColor RGB | 漫反射反照率 |
| BaseColor A | 不透明度 |
| Specular R | 界面 X 方向粗糙度 |
| Specular G | 界面 Y 方向粗糙度 |
| Normal | 3通道标准法线贴图或 2通道 BC5 格式 |

## 类与接口

### `PBRTCoatedDiffuseMaterial`

- **继承**: `BasicMaterial`
- **职责**: 管理带涂层漫反射材质的参数设置、着色器模块加载和 UI 渲染

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<PBRTCoatedDiffuseMaterial> create(ref<Device>, const std::string&)` | 工厂方法，创建材质实例 |
| `ProgramDesc::ShaderModuleList getShaderModules() const override` | 返回着色器模块，指向 `Rendering/Materials/PBRT/PBRTCoatedDiffuseMaterial.slang` |
| `TypeConformanceList getTypeConformances() const override` | 注册为 `IMaterial` 类型 |
| `void setRoughness(float2 roughness)` | 设置界面粗糙度（X, Y） |
| `float2 getRoughness() const` | 获取界面粗糙度 |
| `void renderSpecularUI(Gui::Widgets&) override` | 渲染高光参数 UI |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mData.specular[0..1]` | `float16_t` | 界面 X/Y 粗糙度 |

## 依赖关系

### 本文件引用

- `Scene/Material/BasicMaterial.h` — 基类
- `Utils/Scripting/ScriptBindings.h` — Python 脚本绑定
- `GlobalState.h` — 全局状态

### 被以下文件引用

- 材质系统（通过 `MaterialType::PBRTCoatedDiffuse`）
- `Rendering/Materials/PBRT/PBRTCoatedDiffuseMaterial.slang`

## 实现细节

- BaseColor 纹理槽标记为 `sRGB = true`，启用 sRGB 颜色空间转换
- Specular 纹理槽仅使用红色和绿色通道
- 粗糙度以 `float16_t` 精度存储，修改时触发 `DataChanged` 更新标志
- 提供 Python 绑定，暴露 `roughness` 属性
