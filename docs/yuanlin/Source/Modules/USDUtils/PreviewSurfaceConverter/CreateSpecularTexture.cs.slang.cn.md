# CreateSpecularTexture.cs.slang 源码文档

> 路径: `Source/Modules/USDUtils/PreviewSurfaceConverter/CreateSpecularTexture.cs.slang`
> 类型: Slang 计算着色器
> 模块: USDUtils / PreviewSurfaceConverter

## 功能概述

GPU 计算着色器，将粗糙度和金属度输入（可以分别为纹理或常量值）合并为单张 ORM（Occlusion-Roughness-Metallic）纹理。输出格式为 `float4(0, roughness, metallic, 1)`。

## 结构体与接口

### 常量缓冲区 `CB`

| 成员 | 类型 | 说明 |
|------|------|------|
| `roughnessChannel` | `int` | 粗糙度纹理通道索引（<0 表示使用常量值） |
| `roughnessValue` | `float` | 粗糙度常量值 |
| `metallicChannel` | `int` | 金属度纹理通道索引（<0 表示使用常量值） |
| `metallicValue` | `float` | 金属度常量值 |
| `outDim` | `uint2` | 输出纹理尺寸 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void createSpecularTexture(uint3 threadId)` | 入口点。线程组 [16,16,1]。对每个像素采样粗糙度/金属度纹理（或使用常量值），输出 ORM 纹理 |

## 依赖关系 / import

- `SampleTexture` - 提供 `sampleTexture()` 辅助函数，处理纹理采样时的分辨率适配
