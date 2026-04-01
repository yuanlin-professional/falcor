# ToneMapping.ps.slang 源码文档

> 路径: `Source/RenderPasses/ToneMapper/ToneMapping.ps.slang`
> 类型: 像素着色器 (Pixel Shader)
> 模块: RenderPasses/ToneMapper

## 功能概述

该像素着色器执行色调映射的主要渲染逻辑。按照以下流程处理每个像素：自动曝光调整（可选）-> 颜色分级（白平衡 + 曝光变换）-> 色调映射算子应用 -> 输出钳制（可选）。支持六种色调映射算子。

## 结构体与接口

### 常量与宏定义

| 名称 | 值 | 说明 |
|------|------|------|
| `kOperator` | `_TONE_MAPPER_OPERATOR` | 编译时选择的色调映射算子 |
| `kExposureKey` | `0.042` | 自动曝光的曝光常数 |
| `kLuminanceLod` | `16.0` | 亮度纹理的最高 mip 级别（用于获取平均亮度） |

### 常量缓冲区

| 名称 | 类型 | 说明 |
|------|------|------|
| `gParams` | `ToneMapperParams` | 色调映射参数（whiteScale、whiteMaxLuminance、colorTransform） |

### 资源绑定

| 名称 | 类型 | 说明 |
|------|------|------|
| `gLuminanceTexSampler` | `SamplerState` | 亮度纹理采样器 |
| `gColorSampler` | `SamplerState` | 颜色纹理采样器 |
| `gColorTex` | `Texture2D` | 输入颜色纹理 |
| `gLuminanceTex` | `Texture2D` | 亮度纹理（自动曝光模式） |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float calcLuminance(float3 color)` | 计算亮度（Rec.601 系数） |
| `float3 toneMapLinear(float3 color)` | 线性映射（直通） |
| `float3 toneMapReinhard(float3 color)` | Reinhard 色调映射 |
| `float3 toneMapReinhardModified(float3 color)` | 带最大白色亮度的 Reinhard |
| `float3 toneMapHejiHableAlu(float3 color)` | Hable 的 ALU 近似胶片映射 |
| `float3 applyUc2Curve(float3 color)` | Uncharted 2 曲线函数 |
| `float3 toneMapHableUc2(float3 color)` | Hable 的 Uncharted 2 胶片映射 |
| `float3 toneMapAces(float3 color)` | ACES 胶片映射 |
| `float3 toneMap(float3 color)` | 根据 kOperator 分发到对应映射函数 |
| `float4 main(float2 texC: TEXCOORD) : SV_TARGET0` | 主入口 |

## 依赖关系/import

| 模块 | 说明 |
|------|------|
| `RenderPasses.ToneMapper.ToneMapperParams` | 色调映射参数结构体 |

## 实现细节

1. **自动曝光**: 通过 `_TONE_MAPPER_AUTO_EXPOSURE` 宏定义条件编译。从亮度纹理最高 mip 级别采样获取平均对数亮度，转换为线性平均亮度后应用曝光常数。

2. **颜色分级**: 将颜色乘以 `gParams.colorTransform` 矩阵（包含白平衡和手动曝光调整）。

3. **色调映射算子**:
   - **Linear**: 直通，不做映射
   - **Reinhard**: 经典 `L / (L + 1)` 公式，保持亮度比例
   - **ReinhardModified**: 添加最大白色亮度参数，允许烧白
   - **HejiHableAlu**: John Hable 对 Jim Heji 胶片响应的 ALU 近似，结果包含 sRGB 转换（内部再做 gamma 2.2 补偿）
   - **HableUc2**: Uncharted 2 使用的胶片映射，带肩部/趾部曲线参数
   - **ACES**: ACES 胶片映射曲线（Narkowicz 近似），应用 0.6 预曝光修正

4. **输出钳制**: 通过 `_TONE_MAPPER_CLAMP` 宏条件编译，使用 `saturate` 将结果钳制到 [0, 1]。

5. **Alpha 保留**: 输出保留输入的 alpha 通道值。
