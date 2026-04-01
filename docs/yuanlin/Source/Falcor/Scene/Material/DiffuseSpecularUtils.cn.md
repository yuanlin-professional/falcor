# DiffuseSpecularUtils 源码文档
> 路径: `Source/Falcor/Scene/Material/DiffuseSpecularUtils.h` + `DiffuseSpecularUtils.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Material

## 功能概述
提供漫反射/高光 BRDF 近似参数的加载和 UI 渲染工具。用于加载 MERL BRDF 的 JSON 伴随文件中的最佳拟合参数（baseColor、roughness、metallic、specular），并提供 UI 编辑功能。

## 类与接口

### `DiffuseSpecularUtils`
- **继承**: 无
- **职责**: 加载和编辑 DiffuseSpecular 拟合参数

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `static bool loadJSONData(const path&, DiffuseSpecularData&)` | 从 JSON 文件加载拟合参数 |
| `static bool renderUI(Gui::Widgets&, DiffuseSpecularData&)` | 渲染参数编辑 UI |

## 依赖关系
### 本文件引用
- `DiffuseSpecularData.slang` - 数据结构定义
- `nlohmann/json` - JSON 解析库

### 被以下文件引用
- `MERLFile.cpp`, `MERLMaterial.cpp`, `MERLMixMaterial.cpp`

## 实现细节
- JSON 中 baseColor 存储为 sRGB 格式（`base_color_srgb`），加载后转换为线性空间
- 加载失败时使用默认参数：baseColor=0.5, roughness=0.5, metallic=0.5
