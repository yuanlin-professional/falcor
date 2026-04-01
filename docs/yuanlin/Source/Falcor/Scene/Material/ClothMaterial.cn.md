# ClothMaterial 源码文档
> 路径: `Source/Falcor/Scene/Material/ClothMaterial.h` + `ClothMaterial.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Material

## 功能概述
实现了布料材质类型，专门用于模拟织物表面的光照效果。支持基础颜色（含不透明度）、粗糙度和法线贴图。布料材质使用专门的双向散射分布函数（BSDF）着色模型，区别于标准 PBR 材质。

## 类与接口

### `ClothMaterial`
- **继承**: `BasicMaterial`
- **职责**: 布料材质实现，提供织物特有的着色行为

#### 纹理通道布局
- BaseColor: RGB=基础颜色, A=不透明度
- Specular: G=粗糙度（其他通道未使用）
- Normal: 3 通道标准法线贴图或 2 通道 BC5 格式

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `static ref<ClothMaterial> create(...)` | 工厂方法 |
| `void setRoughness(float roughness)` | 设置粗糙度 |
| `float getRoughness() const` | 获取粗糙度 |

## 依赖关系
### 本文件引用
- `BasicMaterial.h` - 基础材质基类
- `Rendering/Materials/ClothMaterial.slang` - GPU 端着色器

### 被以下文件引用
- 场景加载器创建布料材质实例

## 实现细节
- 仅使用 Specular 纹理的绿色通道存储粗糙度
- 类型一致性声明将 `ClothMaterial` 映射到 `IMaterial` 接口，类型 ID 为 `MaterialType::Cloth`
