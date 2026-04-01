### [Index](../index.md) | [Usage](./index.md) | 场景创建

--------

# 场景创建

场景使用 `SceneBuilder` 类创建。Falcor 使用 Assimp 作为其核心模型加载功能，因此支持 Assimp 支持的所有格式。完整列表请参阅 Assimp 项目文档：https://github.com/assimp/assimp。

除了网格几何体外，Falcor 还支持从模型文件中加载蒙皮动画、动画路径、相机和灯光（点光源/方向光/自发光体）。

有关创建后如何使用 `Scene` 的详细信息，请参阅[场景](./scenes.md)页面。

## 从文件加载模型
从文件加载模型的基本用法：
```c++
SceneBuilder::SharedPtr pBuilder = SceneBuilder::create("path/to/model.file");
Scene::SharedPtr pScene = pBuilder->getScene();
```

模型加载函数还提供可选参数，用于从文件创建模型的多个实例。以下示例从文件加载模型并在位置 [-5, 0, 0] 和 [5, 0, 0] 创建两个实例。
```c++
SceneBuilder::InstanceMatrices instances = {
    glm::translate(float3(-5.0f, 0.0f, 0.0f)),
    glm::translate(float3(5.0f, 0.0f, 0.0f))
};

SceneBuilder::SharedPtr pBuilder = SceneBuilder::create("path/to/model.file", SceneBuilder::Flags::Default. instances);
Scene::SharedPtr pScene = pBuilder->getScene();
```

在创建场景之前，还可以使用 `SceneBuilder` 接口添加未作为模型文件一部分加载的附加场景对象。
```c++
SceneBuilder::SharedPtr pBuilder = SceneBuilder::create("path/to/model.file");
pBuilder->addLight(PointLight::create());
Scene::SharedPtr pScene = pBuilder->getScene();
```

## 从自定义几何体创建场景
在某些情况下，从编程定义的几何体创建场景更为合适。`SceneBuilder` 提供了 `Mesh` 结构体接口，用于将网格数据传递给构建器。构建器会立即创建网格数据的内部副本，因此不需要在最终场景创建之前保持内存有效。
```c++
SceneBuilder::SharedPtr pBuilder = SceneBuilder::create();

SceneBuilder::Mesh mesh;
// ... 填充 mesh 结构体 ...
size_t meshId = pBuilder->addMesh(mesh);
size_t nodeId = pBuilder->addNode(Node( /* 实例矩阵 */ ));
pBuilder->addMeshInstance(nodeId, meshId);

Scene::SharedPtr pScene = pBuilder->getScene();
```

仅将网格添加到场景不足以渲染它。您还必须在场景图中定义一个变换节点（在简单情况下就是网格的世界矩阵），然后添加一个网格实例将网格几何体与变换关联起来。
