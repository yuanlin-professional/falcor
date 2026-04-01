### [Index](../index.md) | [Usage](./index.md) | 场景格式

--------

# 场景格式

## USD 场景文件

从 5.0 版本开始，Falcor 包含了一个实验性的 Universal Scene Description (USD) 文件导入器。

`UsdPreviewSurface` 材质模型部分受支持，在加载时映射到 Falcor 的 `StandardMaterial`。

## FBX/GLTF 场景文件

Falcor 使用 [Assimp](https://github.com/assimp/assimp) 作为 FBX 和 GLTF 场景的资产加载器。它也可以默认加载 Assimp 支持的所有其他文件格式，但支持可能更有限。

所有加载的材质数据在加载时映射到 Falcor 的 `StandardMaterial`。

Falcor 将从资产中导入：
- 场景图
- 网格
- 材质
    - 漫反射纹理
        - Metal-Rough 着色模型（默认）
            - RGB：基础颜色
            - A：不透明度（alpha）
        - Spec-Gloss 着色模型（仅 OBJ 默认）
            - RGB：漫反射颜色
            - A：不透明度（alpha）
    - 高光参数纹理
        - Metal-Rough 着色模型（默认）
            - R：遮蔽（不支持）
            - G：粗糙度
            - B：金属度
        - Spec-Gloss 着色模型（仅 OBJ 默认）
            - RGB：高光颜色
            - A：光泽度
    - 法线纹理
    - 自发光颜色/纹理
- 相机
- 点光源
- 方向光
- 关键帧动画
- 蒙皮动画


## Python 场景文件

您还可以利用 Falcor 的脚本系统来构建场景。这对于从零开始构建简单场景以及在加载时修改现有资产（例如更改材质属性、添加灯光等）非常有用。Python 场景文件使用 `.pyscene` 文件扩展名。

### 基本用法

执行 Python 场景文件时，脚本可以访问类型为 `SceneBuilder` 的全局变量 `sceneBuilder`。顾名思义，`SceneBuilder` 类用于构建场景。完整参考请参阅[脚本文档](./scripting.md)。

一个非常基本的 Python 场景文件可能只是加载一个资产：

```python
# 加载资产
sceneBuilder.importScene('BistroInterior.fbx')
```

如果脚本中没有添加其他内容，其行为与直接加载 `BistroInterior.fbx` 相同。但我们可以做更多。例如，添加一个环境贴图：

```python
# 创建环境贴图
envMap = EnvMap('envmap.hdr')
envMap.intensity = 2.0
sceneBuilder.envMap = envMap
```

我们还可以更改 `BistroInterior.fbx` 中现有材质的属性：

```python
# 更改玻璃材质
m = sceneBuilder.getMaterial('Glass')
m.specularTransmission = 1.0
m.indexOfRefraction = 1.52
```

我们可以添加新的光源：

```python
# 创建方向光
light = DirectionalLight('DirLight0')
light.intensity = float3(3.0, 2.0, 3.0)
light.direction = float3(0.0, -1.0, 0.0)
sceneBuilder.addLight(light)
```

我们可以添加新相机并选择它：

```python
# 创建相机
camera = Camera('Camera0')
camera.position = float3(0.0, 1.0, 10.0)
camera.target = float3(0.0, 1.0, 0.0)
camera.up = float3(0.0, 1.0, 0.0)
camera.focalLength = 50.0
sceneBuilder.addCamera(camera)
sceneBuilder.selectedCamera = camera
```

### 高级用法

除了修改和扩展现有场景外，Python 场景文件还可以用于程序化创建场景。这对调试很有用，但不是作为加载大型资产的导入器的替代品。加载性能和功能有限。

#### 创建几何体

我们从创建一些几何体开始：

```python
# 创建三角形网格
quadMesh = TriangleMesh.createQuad()
cubeMesh = TriangleMesh.createCube()
sphereMesh = TriangleMesh.createSphere()
```

这将创建标准图元的简单三角形网格。您也可以通过加载文件来创建三角形网格，例如从 Wavefront OBJ 文件：

```python
# 从文件创建三角形网格
bunnyMesh = TriangleMesh.createFromFile('Bunny.obj')
```

请注意，`TriangleMesh` 不了解材质，只从文件中加载几何体。我们稍后会定义材质。

最后一个选项是完全从头创建三角形网格：

```python
# 从头创建三角形网格
customMesh = TriangleMesh()
normal = float3(0, 1, 0)
customMesh.addVertex(float3(-10, 0, -10), normal, float2(0, 0))
customMesh.addVertex(float3(10, 0, -10), normal, float2(5, 0))
customMesh.addVertex(float3(-10, 0, 10), normal, float2(0, 5))
customMesh.addVertex(float3(10, 0, 10), normal, float2(5, 5))
customMesh.addTriangle(2, 1, 0)
customMesh.addTriangle(1, 2, 3)
```

每个顶点都有 _position_、_normal_ 和 _texCoord_ 属性。三角形通过索引顶点来定义。

#### 创建材质

接下来我们需要为网格定义至少一个材质。
更多示例请参阅[材质](./materials.md)。

```python
# 创建材质
red = StandardMaterial('Red')
red.baseColor = float4(0.8, 0.1, 0.1, 1.0)
red.roughness = 0.3

green = StandardMaterial('Green')
green.baseColor = float4(0.1, 0.8, 0.1, 1.0)
green.roughness = 0.2
green.emissiveColor = float3(1, 1, 1)
green.emissiveFactor = 0.1

blue = StandardMaterial('Blue')
blue.baseColor = float4(0.1, 0.1, 0.8, 1.0)
blue.roughness = 0.3
blue.metallic = 1

emissive = StandardMaterial('Emissive')
emissive.baseColor = float4(1, 1, 1, 1)
emissive.roughness = 0.2
emissive.emissiveColor = float3(1, 1, 1)
emissive.emissiveFactor = 20
```

我们还可以使用纹理创建材质：

```python
# 创建带纹理的材质
floor = StandardMaterial('Floor')
floor.loadTexture(MaterialTextureSlot.BaseColor, 'Arcade/Textures/CheckerTile_BaseColor.png')
floor.loadTexture(MaterialTextureSlot.Specular, 'Arcade/Textures/CheckerTile_Specular.png')
floor.loadTexture(MaterialTextureSlot.Normal, 'Arcade/Textures/CheckerTile_Normal.png')
```

#### 构建场景

有了一些几何体和材质后，我们现在可以将网格注册到场景构建器：

```python
# 将网格添加到场景构建器
quadMeshID = sceneBuilder.addTriangleMesh(quadMesh, red)
cubeMeshID = sceneBuilder.addTriangleMesh(cubeMesh, emissive)
sphereMeshID = sceneBuilder.addTriangleMesh(sphereMesh, blue)
bunnyMeshID = sceneBuilder.addTriangleMesh(bunnyMesh, green)
customMeshID = sceneBuilder.addTriangleMesh(customMesh, floor)
```

每次调用 `addTriangleMesh()` 都会返回一个新的 ID，唯一标识该网格和分配的材质。

接下来，我们需要创建一些场景图节点：

```python
# 将场景图添加到场景构建器
quadNodeID = sceneBuilder.addNode('Quad', Transform(scaling=2, translation=float3(-3, 1, 0), rotationEulerDeg=float3(90, 0, 0)))
cubeNodeID = sceneBuilder.addNode('Cube', Transform(scaling=float3(15, 0.2, 0.2), translation=float3(0, 1, -2)))
sphereNodeID = sceneBuilder.addNode('Sphere', Transform(scaling=1, translation=float3(3, 1, 0)))
customNodeID = sceneBuilder.addNode('Custom', Transform(scaling=1, rotationEulerDeg=float3(0, 45, 0)))
bunnyNodeID = sceneBuilder.addNode('Bunny', Transform(scaling=12, translation=float3(0.3, -0.4, 0)), customNodeID)
```

每次调用 `addNode()` 都会返回一个唯一标识该节点的新 ID。通过使用 `addNode()` 中的（可选）最后一个参数，我们可以将节点附加到父节点并构建场景层次结构。

`Transform` 类用于描述场景节点的相对变换。构造 `Transform` 时，我们可以直接传入 `translation`、`scaling`、`rotationEuler` 或 `rotationEulerDeg`。或者，我们可以使用 `position`、`target` 和 `up` 创建 _look-at_ 变换。

有了基本的场景图后，我们可以将网格实例附加到它上面：

```python
# 将网格实例添加到场景图
sceneBuilder.addMeshInstance(quadNodeID, quadMeshID)
sceneBuilder.addMeshInstance(cubeNodeID, cubeMeshID)
sceneBuilder.addMeshInstance(sphereNodeID, sphereMeshID)
sceneBuilder.addMeshInstance(bunnyNodeID, bunnyMeshID)
sceneBuilder.addMeshInstance(customNodeID, customMeshID)
```

与基本示例一样，我们显然还应该添加相机和至少一个光源，以使场景可渲染。

```python
# 添加相机
camera = Camera('Camera')
camera.position = float3(0, 0.3, 10)
camera.target = float3(0, 0.3, 0)
camera.up = float3(0, 1, 0)
camera.focalLength = 35
sceneBuilder.addCamera(camera)

# 添加灯光
light0 = DistantLight('Light0')
light0.direction = float3(1, -1, -1)
light0.intensity = float3(5, 5, 1)
light0.angle = 0.1
sceneBuilder.addLight(light0)

light1 = DistantLight('Light1')
light1.direction = float3(-1, -1, -1)
light1.intensity = float3(1, 5, 5)
light1.angle = 0.1
sceneBuilder.addLight(light1)
```

完成所有这些后，我们可以渲染场景并得到以下图像：

![示例场景](images/example-scene.png)

更多 Python 场景示例可以在 `media/TestScenes` 文件夹中找到。
