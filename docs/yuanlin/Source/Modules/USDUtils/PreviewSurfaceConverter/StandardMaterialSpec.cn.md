# StandardMaterialSpec 源码文档

> 路径: `Source/Modules/USDUtils/PreviewSurfaceConverter/StandardMaterialSpec.h`
> 类型: C++ 头文件（仅头文件）
> 模块: USDUtils / PreviewSurfaceConverter

## 功能概述

定义 `StandardMaterialSpec` 结构体，作为构建 `Falcor::StandardMaterial` 之前的中间规格表示。可被哈希以用于材质去重（通过 `ConvertedMaterialCache`），避免从不同 `UsdShadeShader` 创建参数完全相同的材质实例。

## 类与接口

### `StandardMaterialSpec`

- **职责**: 保存构建 `StandardMaterial` 所需的全部参数。

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `name` | `std::string` | 材质名称 |
| `texTransform` | `ConvertedTexTransform` | 共享纹理坐标变换 |
| `baseColor` | `ConvertedInput` | 基础颜色 |
| `normal` | `ConvertedInput` | 法线贴图 |
| `metallic` | `ConvertedInput` | 金属度 |
| `roughness` | `ConvertedInput` | 粗糙度 |
| `opacity` | `ConvertedInput` | 不透明度（默认 1.0） |
| `emission` | `ConvertedInput` | 自发光 |
| `disp` | `ConvertedInput` | 位移贴图 |
| `volumeAbsorption` | `ConvertedInput` | 体积吸收系数 |
| `volumeScattering` | `ConvertedInput` | 体积散射系数 |
| `opacityThreshold` | `float` | 不透明度阈值（默认 0） |
| `ior` | `float` | 折射率（默认 1.5） |

### `StandardMaterialSpecHash`

- **职责**: 为 `StandardMaterialSpec` 提供哈希函数，使用 `hash_combine` 组合所有成员的哈希值。

## 依赖关系

### 本文件引用

- `Core/API/Texture.h`、`Sampler.h` - 纹理/采样器类型
- `Scene/Material/StandardMaterial.h` - 标准材质
- `USDUtils/USDUtils.h` - `hash_combine`
- `USDUtils/ConvertedInput.h` - `ConvertedInput`、`ConvertedTexTransform`

### 被以下文件引用

- `PreviewSurfaceConverter.h` - 材质缓存的模板参数
