# HairMaterial 源码文档
> 路径: `Source/Falcor/Scene/Material/HairMaterial.h` + `HairMaterial.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Material

## 功能概述
实现了头发/毛发材质类型。基于物理的头发渲染模型，使用吸收系数 (sigmaA) 描述颜色，支持纵向粗糙度 (betaM)、方位角粗糙度 (betaN) 和鳞片倾斜角等参数。

## 类与接口

### `HairMaterial`
- **继承**: `BasicMaterial`
- **职责**: 头发材质实现，提供基于物理的毛发着色

#### 纹理通道布局
- BaseColor: RGB=吸收系数 sigmaA（注意：不支持 Alpha 通道）
- Specular: R=纵向粗糙度 betaM, G=方位角粗糙度 betaN, B=鳞片倾斜角（度）

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `static float3 sigmaAFromConcentration(float ce, float cp)` | 从真黑色素和棕黑色素浓度计算吸收系数 |
| `static float3 sigmaAFromColor(float3 color, float betaN)` | 从 RGB 颜色和方位角粗糙度计算吸收系数 |
| `static float3 colorFromSigmaA(float3 sigmaA, float betaN)` | 从吸收系数计算 RGB 颜色（逆函数） |

## 依赖关系
### 本文件引用
- `BasicMaterial.h` - 基础材质基类
- `Rendering/Materials/HairMaterial.slang` - GPU 端着色器

## 实现细节
- 头发材质不支持 Alpha 测试（BaseColor 仅使用 RGB 通道）
- `sigmaAFromConcentration` 基于真黑色素和棕黑色素的经验吸收系数计算
- 颜色与吸收系数的转换使用经验公式，依赖于方位角粗糙度参数
