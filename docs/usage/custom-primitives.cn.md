### [Index](../index.md) | [Usage](./index.md) | 自定义相交图元

--------

# 自定义图元
使用 DXR 时支持自定义相交图元。要渲染它们，您必须：
1. 定义包含对象的包围盒。
2. 为要渲染的每种图元类型实现 intersection shader。
3. 实现额外的 hit shader 来处理自定义图元。

## 向场景中添加自定义图元
目前，自定义图元必须通过 `SceneBuilder` 手动添加：
```c++
auto pSceneBuilder = SceneBuilder::create(path);
pSceneBuilder->addCustomPrimitive(0, AABB(float3(-0.5f), float3(0.5f)));
```

该接口包含两个参数：第一个是索引，指定哪个 intersection shader 应处理此图元（详见下文）；第二个是描述图元边界的 AABB 对象。

## Shader 设置
在 Falcor 中，自定义相交图元的 hit group 与处理三角形网格的 hit group 是分开声明的。Intersection shader 本身也是分开的，尽管 DXR 将它们视为 hit group 的一部分。

```c++
RtProgram::Desc desc;
// 三角形网格的 shader
desc.addShaderLibrary("Shaders.rt.slang").setRayGen("rayGen");
desc.addHitGroup(0, "scatterClosestHit", "").addMiss(0, "scatterMiss");
// 自定义图元的 shader
desc.addIntersection(0, "myIntersection");
desc.addAABBHitGroup(0, "isectScatterClosestHit", "");
```

`addIntersection()` 接受一个索引标识符参数，类似于 `addHitGroup()` 和 `addMiss()` 中的 `hitIndex` 和 `missIndex`。在 `SceneBuilder` 中添加自定义图元时，`typeID` 选择将运行哪个 intersection shader，与在 `addIntersection()` 中的声明方式对应。例如，每个使用 `addCustomPrimitive(0, ...)` 声明的自定义图元，都将由声明为 `addIntersection(0, ...")` 的 intersection shader 处理，以此类推。

**用户还必须编写额外的 hit group（Closest Hit 和/或 Any Hit shader）来处理 intersection shader，并使用 `addAABBHitGroup()` 声明它们。** 这是因为 intersection shader 可以向 hit shader 输出用户定义的命中属性，而三角形网格的 hit shader 始终使用 `BuiltInTriangleIntersectionAttributes`。在自定义 intersection shader 和 hit group 中使用 `BuiltInTriangleIntersectionAttributes` 是有效的，但目前您仍然必须实现单独的 hit shader。**所有处理自定义图元的 intersection shader 和 hit shader 必须共享相同的属性结构体。**

通常，每个"AABB Hit Group"应与其对应的"Triangle Hit Group"行为匹配，因为 shader 中 `TraceRay()` 参数指定的光线索引（`RayContributionToHitGroupIndex`）对两种 hit group 类型的影响相同。例如，光线索引 0 处的两个 hit group 都是散射光线，索引 1 处的两个 hit group 都是阴影光线，等等。自定义相交没有单独的 miss shader。

## 编写 Shader
有关 intersection shader 的 HLSL 语法详情，请参阅 DXR 文档 [此处](https://microsoft.github.io/DirectX-Specs/d3d/Raytracing.html#intersection-shader)。

在 shader 端，用户定义的自定义图元由其世界空间的最小和最大点、typeID 及实例索引表示。这些都存储在场景数据结构中，形式为 `StructuredBuffer<CustomPrimitiveData> customPrimitives;`。

实例索引用于区分同一类型的图元。它从零开始，按每种类型分别计数。通过 `typeID` 和 `instanceIdx`，用户可以唯一标识自定义图元并查找渲染所需的附加数据。

要访问当前正在处理的自定义图元的场景数据，请使用
```c++
gScene.customPrimitives[GeometryIndex()];
```

