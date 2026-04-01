# MaterialFactory.slang 着色器文档
> 路径: `Source/Falcor/Scene/Material/MaterialFactory.slang`
> 类型: Slang 着色器
> 模块: Scene/Material

## 功能概述
为 `MaterialSystem` 添加扩展方法，提供材质工厂和着色数据准备功能。通过 Slang 的 `extension` 机制避免循环依赖。所有纹理采样函数接受实现了 `ITextureSampler` 接口的 LOD 计算方法。

## 结构体与接口

### `extension MaterialSystem`

## 函数

| 函数签名 | 说明 |
|----------|------|
| `IMaterial getMaterial(uint materialID)` | 根据材质 ID 创建 `IMaterial` 动态对象实例 |
| `IMaterialInstance getMaterialInstance<L:ITextureSampler>(ShadingData, L lod, uint hints)` | 创建材质实例，执行纹理采样和着色帧计算 |
| `ShadingData prepareShadingData(VertexData v, uint materialID, float3 viewDir)` | 在命中点准备着色数据 |

## 依赖关系
### import
- `Rendering.Materials.IMaterial` - 材质接口
- `Scene.Material.ShadingUtils` - 着色工具
- `Utils.Math.ShadingFrame` - 着色帧

## 实现细节
- `getMaterial()` 使用 `createDynamicObject` 根据材质类型动态创建对应的 `IMaterial` 实现
- `prepareShadingData()` 从插值顶点数据构建着色数据，包括着色帧（基于法线和切线）、面法线朝向判断等
- 默认假设前面的折射率为 1.0（真空），渲染器可为嵌套电介质覆盖此值
