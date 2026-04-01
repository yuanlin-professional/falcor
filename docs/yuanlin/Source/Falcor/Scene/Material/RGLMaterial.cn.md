# RGLMaterial 源码文档
> 路径: `Source/Falcor/Scene/Material/RGLMaterial.h` + `RGLMaterial.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Material

## 功能概述
实现了基于 RGL BRDF 数据库的测量材质。RGL 使用自适应参数化方法高效获取和渲染材质外观，支持各向异性 BRDF。

参考文献：Jonathan Dupuy, Wenzel Jakob, "An Adaptive Parameterization for Efficient Material Acquisition and Rendering", SIGGRAPH Asia 2018.

## 类与接口

### `RGLMaterial`
- **继承**: `Material`
- **职责**: 基于 RGL 测量数据的材质实现

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `bool loadBRDF(const path& path)` | 加载 RGL BRDF 文件 |
| `size_t getMaxBufferCount() const` | 返回 12（使用大量缓冲区存储各类查找表） |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `mpThetaBuf`, `mpPhiBuf` | `ref<Buffer>` | 入射角采样点缓冲区 |
| `mpSigmaBuf`, `mpNDFBuf` | `ref<Buffer>` | 投影微面积和 NDF 缓冲区 |
| `mpVNDFBuf`, `mpLumiBuf`, `mpRGBBuf` | `ref<Buffer>` | VNDF、亮度和 RGB 测量数据缓冲区 |
| `mpVNDFMarginalBuf`, `mpLumiMarginalBuf` | `ref<Buffer>` | VNDF/亮度边际 CDF 缓冲区 |
| `mpVNDFConditionalBuf`, `mpLumiConditionalBuf` | `ref<Buffer>` | VNDF/亮度条件 CDF 缓冲区 |
| `mpAlbedoLUT` | `ref<Texture>` | 反照率查找表纹理 |

## 依赖关系
### 本文件引用
- `Material.h`, `RGLMaterialData.slang`, `RGLFile.h`, `RGLCommon.h`
- `Rendering/Materials/BSDFIntegrator.h` - 计算反照率 LUT

## 实现细节
- 加载时创建 `SamplableDistribution4D` 分布用于 VNDF 和亮度的采样
- 分辨率上限为 `kMaxResolution = 0xffff`
- 反照率 LUT 缓存为 DDS 文件，大小 256 元素
- `update()` 中支持缓冲区替换（`replaceBuffer`），避免重新加载时增加缓冲区数量
