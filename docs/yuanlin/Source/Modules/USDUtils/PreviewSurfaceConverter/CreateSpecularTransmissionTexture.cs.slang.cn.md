# CreateSpecularTransmissionTexture.cs.slang 源码文档

> 路径: `Source/Modules/USDUtils/PreviewSurfaceConverter/CreateSpecularTransmissionTexture.cs.slang`
> 类型: Slang 计算着色器
> 模块: USDUtils / PreviewSurfaceConverter

## 功能概述

GPU 计算着色器，将不透明度纹理转换为镜面透射纹理。对每个像素执行 `1 - opacity` 运算，输出灰度透射纹理。

## 结构体与接口

### 常量缓冲区 `CB`

| 成员 | 类型 | 说明 |
|------|------|------|
| `opacityChannel` | `int` | 不透明度数据所在的纹理通道索引 |
| `outDim` | `uint2` | 输出纹理尺寸 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void createSpecularTransmissionTexture(uint3 threadId)` | 入口点。线程组 [16,16,1]。读取不透明度通道值，输出 `float4(1-opacity, 1-opacity, 1-opacity, 1)` |

## 依赖关系 / import

- 无外部 import（直接使用纹理下标访问）
