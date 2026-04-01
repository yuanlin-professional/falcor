### [Index](../index.md) | [Usage](./index.md) | 材质

--------

# 材质

从 Falcor 5.0 开始，引入了新的材质系统，允许在运行时创建和渲染不同的材质类型。在之前的版本中，使用的是固定的漫反射+GGX 材质模型。

所有材质及其资源由 `MaterialSystem` C++ 类和对应的 Slang 模块管理。材质系统由 `Scene` 对象拥有，并在渲染时与其他资源一起绑定。

要在 shader 代码中访问材质系统，必须导入以下模块：

```c++
import Scene.Shading;
```

## 数据布局

为了高效访问，每个材质由固定大小的数据块描述（当前为 128B）。数据块由头部（参见 `Falcor/Scene/Material/MaterialData.slang` 中声明的 `MaterialHeader`）和数据负载组成。头部始终存在，包含材质类型 ID 和辅助标志等。

数据负载的格式是不透明的，取决于材质类型。如果材质需要的数据超出负载容量，可以在缓冲区中存储额外的旁路数据。

所有资源（纹理、采样器、缓冲区）通过无绑定资源 ID 访问，其中实际的 GPU 资源由 `MaterialSystem` 管理，ID 在添加资源时分配。这些无绑定 ID 作为数据负载的一部分存储，因此材质是自包含的，完全由其数据块描述。

## 材质类（主机端）

在主机端，所有材质派生自 `Scene/Material/Material.h` 中声明的 `Material` 基类。

要添加新材质，应添加一个继承自 `Material` 的新类并实现其纯虚成员函数。最重要的函数是：

```c++
// 每帧调用一次，准备材质以进行渲染。
Material::UpdateFlags Material::update(MaterialSystem* pOwner);

// 返回材质数据块。
MaterialDataBlob Material::getDataBlob() const;
```

基类持有 `MaterialHeader` 结构体，派生材质类负责持有数据负载。`getDataBlob()` 返回最终的数据块，材质系统会将其上传到 GPU 以供 shader 端访问。

## Python 绑定

为了允许通过 Python 脚本（包括 `.pyscene` 文件）创建材质和设置参数，每个材质类都应导出 Python 绑定。

这些绑定定义在 `FALCOR_SCRIPT_BINDING` 块中，通常放在材质的 `.cpp` 文件底部。

使用示例：

```c++
glass = StandardMaterial("WindowGlass")
glass.roughness = 0
glass.metallic = 0
glass.indexOfRefraction = 1.52
glass.specularTransmission = 1
glass.doubleSided = True
glass.nestedPriority = 2
glass.volumeAbsorption = float3(2.0, 1.0, 1.5)
```

有关如何通过 Python 创建材质的更多示例，请参阅 `media/TestScenes/` 中的测试场景（此目录在项目设置期间会自动获取）。

## 材质模块（shader 端）

在 shader 端，每个材质类都有一个对应的 Slang 模块，存储在 `Falcor/Rendering/Materials/` 中。这些模块实现了 `IMaterial` Slang 接口（参见 `Rendering/Materials/IMaterial.slang`）。

材质模块的主要用途是：
1. 持有材质数据，以及
2. 持有在着色点设置 BSDF 的代码。

后者被称为"图案生成"，可能涉及采样纹理、评估过程函数以及着色所需的任何其他设置。

材质模块中的第一个数据字段必须是材质头部。之后应跟随为该材质类型声明的材质负载。例如，标准材质声明如下：

```c++
struct StandardMaterial : MaterialBase, IMaterial
{
    // MaterialHeader header; -- 来自 MaterialBase
    BasicMaterialData data;
    ...
};
```

通过如下方式调用材质系统来创建材质实例：

```c++
IMaterial material = gScene.materials.getMaterial(materialID);
```

在内部，此函数访问材质头部以获取材质类型，然后调用 Slang 的 `createDynamicObject<..>` 函数创建正确类型的实例。不透明的材质数据块被转换为材质模块中使用的数据类型，因此其字段在材质模块内部可以直接访问。

## BSDF 模块（shader 端）

每个材质模块都有一个关联的 BSDF 类型，实现了 `IBSDF` Slang 接口。例如，`StandardMaterial` 有一个关联的 `StandardBSDF` 类型。

BSDF 类型的实例针对场景中的特定着色点创建，并提供在该点评估和采样 BSDF 的接口。`IBSDF` 接口还具有查询着色点额外 BSDF 属性的函数，如反照率、自发光等。

通过在材质上调用以下函数来创建 BSDF 实例：

```c++
ShadingData sd = ...       // 描述着色点的结构体
ITextureSampler lod = ...  // 纹理细节层次计算方法

IBSDF bsdf = material.setupBSDF(gScene.materials, sd, lod);
```

在内部，`setupBSDF` 函数访问材质系统以获取/评估着色点所需的所有资源。它返回材质关联的 BSDF 类型的实例，调用者可以使用它在着色点评估或采样 BSDF。

由于创建材质后实例化 BSDF 非常常见，`MaterialSystem` 上有一个便捷函数 `getBSDF()`，可以一步完成两个操作：

```c++
IBSDF bsdf = gScene.materials.getBSDF(sd, lod);
```

在上述接口中，需要一个 `ShadingData` 结构体来描述着色点。这是通过在命中点调用 `prepareShadingData()` 函数生成的。此函数负责设置着色帧（法线、切线、副切线），包括评估法线贴图和 alpha 测试的材质不透明度。

此外，还需要一个 `ITextureSampler` 实例来描述纹理的采样方式（如果有纹理的话）。调用者负责根据使用的纹理 LOD 方法（例如光线锥、光线微分、固定 mip 层级等）来决定。可用选项参见 `Scene/Material/TextureSampler.slang`。
