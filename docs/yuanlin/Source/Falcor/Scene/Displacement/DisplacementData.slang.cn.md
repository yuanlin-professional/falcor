# DisplacementData.slang 源码文档

> 路径: `Source/Falcor/Scene/Displacement/DisplacementData.slang`
> 类型: Slang 着色器文件
> 模块: Scene/Displacement

## 功能概述

定义位移贴图（Displacement Mapping）所需的数据结构 `DisplacementData`，包含位移纹理、采样器以及缩放/偏移参数。该结构体是位移映射系统的核心数据载体，被其他位移相关着色器文件引用。

## 结构体与接口

### `DisplacementData`

位移贴图数据结构，封装了位移纹理资源及其采样配置。

#### 静态常量

| 常量 | 类型 | 值 | 说明 |
|------|------|-----|------|
| `kShellMinMaxMargin` | `float2` | `(-0.0001, 0.0001)` | 外壳（shell）最小/最大值的边距，用于避免数值精度问题 |
| `kSurfaceSafetyScaleBias` | `float2` | `(1.0001, 0.0001)` | 表面安全缩放与偏移，用于防止自相交 |

#### 成员变量

| 成员 | 类型 | 说明 |
|------|------|------|
| `texture` | `Texture2D` | 位移纹理，存储高度场数据 |
| `samplerState` | `SamplerState` | 标准采样器，用于读取位移值 |
| `samplerStateMin` | `SamplerState` | 最小值采样器，用于获取保守的最小位移值（通常使用 min-filter MIP 链） |
| `samplerStateMax` | `SamplerState` | 最大值采样器，用于获取保守的最大位移值（通常使用 max-filter MIP 链） |
| `size` | `float2` | 纹理尺寸（以纹素为单位） |
| `scale` | `float` | 位移缩放系数，将纹理值映射到物体空间距离 |
| `bias` | `float` | 位移偏移值，与纹理原始值相加后再乘以 scale |

## 函数

无独立函数。该文件仅定义数据结构，相关的方法扩展在 `DisplacementMapping.slang` 中通过 `extension` 机制添加。

## 依赖关系

### import

- 无 import 依赖

### 被以下文件引用

- `DisplacementMapping.slang`：通过 `extension DisplacementData` 扩展该结构体的方法
- `DisplacementUpdate.cs.slang`：使用该结构体读取位移数据以计算 AABB
- 材质系统：在运行时填充该结构体的纹理和参数

## 实现细节

- 位移值的计算公式为：`displacement = scale * (rawTextureValue + bias)`
- 使用三个不同的采样器支持标准采样、最小值采样和最大值采样，这对于计算保守的外壳包围盒至关重要
- `kShellMinMaxMargin` 为外壳边界提供微小的安全裕度，防止由于浮点精度导致的光线遗漏
- 纹理尺寸 `size` 以纹素为单位存储，便于在纹理空间和 UV 空间之间进行转换
