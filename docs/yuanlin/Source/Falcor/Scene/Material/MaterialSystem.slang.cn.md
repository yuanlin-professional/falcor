# MaterialSystem.slang 着色器文档
> 路径: `Source/Falcor/Scene/Material/MaterialSystem.slang`
> 类型: Slang 着色器
> 模块: Scene/Material

## 功能概述
定义了 GPU 端的材质系统结构体 `MaterialSystem`，持有所有材质数据、纹理、采样器和缓冲区资源。提供材质查询、纹理采样、Alpha 测试、自发光评估、位移贴图加载等核心渲染功能。

## 结构体与接口

### `MaterialSystem`
GPU 端材质系统的核心结构体。

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `materialCount` | `uint` | 材质总数 |
| `materialData` | `StructuredBuffer<MaterialDataBlob>` | 材质参数缓冲区 |
| `materialSamplers` | `SamplerState[]` | 所有材质的采样器状态数组 |
| `materialTextures` | `Texture2D<float4>[]` | 所有材质的 2D 纹理数组 |
| `materialBuffers` | `ByteAddressBuffer[]` | 所有材质的缓冲区数组 |
| `materialTextures3D` | `Texture3D<float4>[]` | 3D 纹理数组 |
| `lightProfile` | `LightProfile` | 全局 IES 光照配置文件 |
| `udimIndirection` | `StructuredBuffer<int>` | UDIM 间接寻址表 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `MaterialType getMaterialType(uint materialID)` | 获取材质类型 |
| `MaterialHeader getMaterialHeader(uint materialID)` | 获取材质头 |
| `bool isBasicMaterial(uint materialID)` | 判断是否为基础材质 |
| `BasicMaterialData getBasicMaterialData(uint materialID)` | 获取基础材质数据 |
| `SamplerState getTextureSampler(uint samplerID)` | 获取纹理采样器 |
| `float4 sampleTexture<L>(TextureHandle, SamplerState, float2 uv, float4 uniformValue, L lod)` | 泛型纹理采样 |
| `bool alphaTest<L>(VertexData v, uint materialID, L lod)` | Alpha 测试 |
| `float3 evalEmissive(uint materialID, float2 uv)` | 评估自发光辐射 |
| `bool isDisplaced(uint materialID)` | 检查是否有位移贴图 |
| `void loadDisplacementData(uint materialID, out DisplacementData)` | 加载位移贴图数据 |
| `TextureHandle getResolvedTextureHandle(TextureHandle, inout float2 uv)` | 解析 UDIM 纹理句柄 |

## 依赖关系
### import
- `Scene.Material.TextureHandle`, `TextureSampler`, `AlphaTest`
- `Scene.Material.BasicMaterialData`, `VolumeProperties`
- `Scene.SceneTypes`, `Scene.Displacement.DisplacementData`
- `Scene.Lights.LightProfile`
- `Rendering.Volumes.PhaseFunction`
