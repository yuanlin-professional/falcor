### [Index](../index.md) | [Usage](./index.md) | 场景

--------

# 场景

Scene 类管理描述场景所需的所有数据。包括：
- 场景图
- 网格
- 灯光
- 摄像机
- 动画（蒙皮和路径）
- 环境贴图
- 加速结构（仅限 DirectX Raytracing）

一旦 Scene 实例被创建，就不能添加或移除对象，但可以修改灯光和摄像机等对象的属性。

## 摄像机

场景可以包含多个摄像机，但只有一个是活动的。从模型文件加载的第一个摄像机将成为活动摄像机。如果没有加载摄像机，将自动创建一个默认摄像机。

场景还提供了使用键盘/鼠标控制摄像机的功能，支持以下模式：
- 第一人称
- 轨道环绕
- 六自由度

默认模式为第一人称，可以使用以下方式更改：
```c++
void Scene::setCameraController(CameraControllerType type);
```

### 控制

| 按键 | 描述 | 模式 |
| --- |-------------|-------|
| **W/A/S/D** | 前进、左移、后退、右移 | 第一人称、六自由度 |
| **Q/E** | 下降、上升 | 六自由度 |
| **左键拖拽** | 转动摄像机 | 第一人称、六自由度 |
| **左键拖拽** | 轨道环绕摄像机 | 轨道环绕 |
| **右键拖拽** | 滚转摄像机 | 六自由度 |
| **Shift** | 增加移动速度 | 第一人称、六自由度 |
| **Ctrl** | 降低移动速度 | 第一人称、六自由度 |


## 集成

**注意：如果您使用 Mogwai，大部分操作会自动处理。您可以跳到下面的 Shader 部分。**

### 每帧更新

如果您的场景包含动画，或者您需要摄像机控制器，***必须***确保从您的 `Renderer` 中调用以下场景函数：
```c++
void YourRenderer::onFrameRender(RenderContext* pRenderContext, const Fbo::SharedPtr& pTargetFbo)
{
    if(mpScene) mpScene->update(pRenderContext, gpFramework->getGlobalClock().now());
}

bool YourRenderer::onKeyEvent(const KeyboardEvent& keyEvent)
{
    return mpScene ? mpScene->onKeyEvent(mouseEvent) : false;
}

bool YourRenderer::onMouseEvent(const MouseEvent& mouseEvent)
{
    return mpScene ? mpScene->onMouseEvent(mouseEvent) : false;
}
```

`Scene::update()` 返回一组标志，指示场景中哪些对象已更改。如果您的渲染器或技术需要根据场景变化重置值、更新资源等，这很有用。有关更多详细信息，请参阅 `IScene::UpdateFlags.h` 中的 `IScene::UpdateFlags`。

### 加速结构

`Scene` 类在内部创建和管理光线追踪加速结构。需要时，底层加速结构在 `Scene::update()` 中更新，顶层加速结构在 `Scene::raytrace()` 中更新。如果不调用 `Scene::raytrace()`，将不会创建光线追踪资源。

加速结构可以通过完全重建或重新拟合现有结构来更新。默认情况下，为了获得最佳通用性能：
- 顶层加速结构**重建**
- 底层加速结构**重新拟合**

可以使用以下方式更改：
```c++
void Scene::setTlasUpdateMode(UpdateMode mode);
void Scene::setBlasUpdateMode(UpdateMode mode);
```

## 渲染

场景可以使用 `Scene` 类中的函数进行渲染。不再有 `SceneRenderer` 类。

要进行光栅化，使用：
```c++
void Scene::rasterize(RenderContext* pContext, GraphicsState* pState, GraphicsVars* pVars, RenderFlags flags = RenderFlags::None);
```

要进行光线追踪，使用：
```c++
void Scene::raytrace(RenderContext* pContext, RtProgram* pProgram, const std::shared_ptr<RtProgramVars>& pVars, uint3 dispatchDims);
```

## Shader

场景的 GPU 数据结构在 `Scene/Scene.slang` 中描述，通过全局变量 `gScene` 访问。还有一些辅助函数用于简化数据查找。

要在 shader 中访问场景，必须在文件顶部导入 `Scene/Scene.slang`：
```
import Scene.Scene;
```

**重要：** 当前场景 GPU 数据结构需要宏定义才能正确工作。编译 shader 时可以从场景本身获取这些定义。
```c++
SceneBuilder::SharedPtr pBuilder = SceneBuilder::create(path, flags);
Scene::SharedPtr pScene = pBuilder->getScene();

GraphicsProgram::SharedPtr pProgram = GraphicsProgram::createFromFile("Shader.3d.slang", "vsMain", "psMain");
pProgram->addDefines(pScene->getSceneDefines()); // 首先从场景添加定义
GraphicsVars::SharedPtr pProgramVars = GraphicsVars::create(pProgram->getReflector()); // 然后使用
```

### 光栅化

默认顶点 shader 的输出包含两个参数：`instanceID` 和 `materialID`，可用于查找当前渲染网格的数据。
请参阅 `Scene/Scene.slang` 中的接口。

对于基本用法，不需要自己执行查找。`Scene/Raster.slang` 中定义的辅助函数可以为您加载和准备数据。

```c++
import Scene.Raster;

float4 main(VSOut vertexOut, float4 pixelCrd : SV_POSITION, uint triangleIndex : SV_PrimitiveID) : SV_TARGET
{
    float3 viewDir = normalize(gScene.camera.getPosition() - vOut.posW);
    let lod = ImplicitLodTextureSampler();
    ShadingData sd = prepareShadingData(vertexOut, triangleIndex, viewDir, lod);
    ...
}
```

### 光线追踪

使用 `Scene/Raytracing.slang` 中名为 `getGeometryInstanceID()` 的辅助函数来计算等效于光栅化中 `instanceID` 的值，可以以相同方式用于查找几何体和材质数据。

```c++
import Scene.Raytracing;

[shader("closesthit")]
void primaryClosestHit(inout PrimaryRayData hitData, in BuiltInTriangleIntersectionAttributes attribs)
{
    GeometryInstanceID instanceID = getGeometryInstanceID();
    VertexData v = getVertexData(instanceID, PrimitiveIndex(), attribs);
    const uint materialID = gScene.getMaterialID(instanceID);
    let lod = ExplicitLodTextureSampler(0.f);
    ShadingData sd = gScene.materials.prepareShadingData(v, materialID, -WorldRayDirection(), lod);
    ...
}
```
