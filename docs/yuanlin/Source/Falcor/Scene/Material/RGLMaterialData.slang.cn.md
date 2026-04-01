# RGLMaterialData.slang 着色器文档
> 路径: `Source/Falcor/Scene/Material/RGLMaterialData.slang`
> 类型: Slang 着色器（主机/设备共享）
> 模块: Scene/Material

## 功能概述
定义了 RGL 测量材质的数据结构 `RGLMaterialData`，包含角度采样尺寸、各类查找表缓冲区 ID 和反照率查找表纹理句柄。RGL 材质使用大量缓冲区存储测量数据和预计算的采样分布。

## 结构体与接口

### `RGLMaterialData`
| 字段 | 类型 | 说明 |
|------|------|------|
| `phiSize`, `thetaSize` | `uint` | 角度采样点数量 |
| `sigmaSize`, `ndfSize` | `uint2` | sigma/NDF 查找表尺寸 |
| `vndfSize`, `lumiSize` | `uint2` | VNDF/亮度查找表尺寸 |
| `thetaBufID`, `phiBufID` | `uint` | 入射角数据缓冲区 ID |
| `sigmaBufID`, `ndfBufID` | `uint` | 投影微面积和 NDF 缓冲区 ID |
| `vndfBufID`, `lumiBufID`, `rgbBufID` | `uint` | VNDF/亮度/RGB 数据缓冲区 ID |
| `vndfMarginalBufID`, `lumiMarginalBufID` | `uint` | 边际 CDF 缓冲区 ID |
| `vndfConditionalBufID`, `lumiConditionalBufID` | `uint` | 条件 CDF 缓冲区 ID |
| `samplerID` | `uint` | LUT 采样器 ID |
| `texAlbedoLUT` | `TextureHandle` | 反照率查找表纹理句柄 |
| `kMaxResolution` | `constexpr uint` | 最大分辨率 = 0xffff |
| `kAlbedoLUTSize` | `constexpr uint` | 查找表大小 = 256 |

## 依赖关系
### import
- `TextureHandle.slang`
