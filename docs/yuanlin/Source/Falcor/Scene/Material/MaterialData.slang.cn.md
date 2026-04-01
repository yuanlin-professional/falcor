# MaterialData.slang 着色器文档
> 路径: `Source/Falcor/Scene/Material/MaterialData.slang`
> 类型: Slang 着色器（主机/设备共享）
> 模块: Scene/Material

## 功能概述
定义了材质系统的核心数据结构：`MaterialHeader`（16 字节材质头）、`MaterialPayload`（112 字节载荷）和 `MaterialDataBlob`（128 字节完整数据块）。这些结构在所有材质类型间共享，用于 CPU-GPU 数据传输。

## 结构体与接口

### `MaterialHeader` (16B)
材质头数据，包含所有材质类型共有的参数，以位域方式紧凑存储。

| 位域 | 位数 | 说明 |
|------|------|------|
| MaterialType | 16 | 材质类型枚举 |
| NestedPriority | 4 | 嵌套电介质优先级 |
| LobeType | 8 | 活跃的双向散射分布函数瓣类型 |
| DoubleSided | 1 | 双面渲染标志 |
| ThinSurface | 1 | 薄表面标志 |
| Emissive | 1 | 自发光标志 |
| IsBasicMaterial | 1 | 基础材质标志 |
| AlphaThreshold | 16 | Alpha 阈值 (float16) |
| AlphaMode | 1 | Alpha 模式 |
| SamplerID | 8 | 默认纹理采样器 ID |
| EnableLightProfile | 1 | IES 光照配置文件启用标志 |
| DeltaSpecular | 1 | Delta 高光标志 |
| IoR | 16 | 折射率 (float16) |
| AlphaTextureHandle | 32 | Alpha 纹理句柄 |

### `MaterialPayload` (112B)
材质载荷数据，格式取决于材质类型，为 28 个 uint 的数组。

### `MaterialDataBlob` (128B)
完整材质数据块，大小对齐到一个缓存行。由 `MaterialHeader` + `MaterialPayload` 组成。

## 依赖关系
### import
- `MaterialTypes.slang`, `TextureHandle.slang`
