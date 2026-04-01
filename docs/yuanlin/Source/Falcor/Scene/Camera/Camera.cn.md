# Camera 源码文档

> 路径: `Source/Falcor/Scene/Camera/Camera.h` + `.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Camera

## 功能概述

实现了 Falcor 的核心相机类，提供透视/正交投影、景深（DOF）、运动模糊支持、视锥体剔除和抖动模式生成等功能。相机支持动画系统，可通过场景图节点驱动变换。每帧开始时调用 `beginFrame` 来追踪参数变更并存储前帧矩阵用于运动向量计算。

## 类与接口

### `Camera`

- **继承**: `Animatable`
- **职责**: 管理相机参数（位置、朝向、投影属性、曝光等），计算视图/投影矩阵，支持光线追踪和光栅化

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<Camera> create(const std::string&)` | 工厂方法 |
| `void setPosition(const float3&)` | 设置世界空间位置 |
| `void setTarget(const float3&)` | 设置目标点 |
| `void setUpVector(const float3&)` | 设置上方向向量 |
| `void setFocalLength(float)` | 设置焦距（毫米） |
| `void setAspectRatio(float)` | 设置宽高比 |
| `void setFocalDistance(float)` | 设置焦点距离（场景单位，用于景深） |
| `void setApertureRadius(float)` | 设置光圈半径（场景单位） |
| `void setDepthRange(float nearZ, float farZ)` | 设置深度范围 |
| `void setPatternGenerator(const ref<CPUSampleGenerator>&, const float2&)` | 设置抖动模式生成器 |
| `void setJitter(float, float)` | 手动设置亚像素抖动 |
| `Ray computeRayPinhole(uint2, uint2, bool) const` | 计算针孔相机射线 |
| `float computeScreenSpacePixelSpreadAngle(uint32_t) const` | 计算像素扩展角（用于 RayCone 纹理 LOD） |
| `const float4x4 getViewMatrix() const` | 获取视图矩阵 |
| `const float4x4 getProjMatrix() const` | 获取投影矩阵 |
| `const float4x4 getViewProjMatrix() const` | 获取视图投影矩阵 |
| `const float4x4 getInvViewProjMatrix() const` | 获取逆视图投影矩阵 |
| `void setProjectionMatrix(const float4x4&)` | 设置持久投影矩阵 |
| `void setViewMatrix(const float4x4&)` | 设置持久视图矩阵 |
| `bool isObjectCulled(const AABB&) const` | 视锥体剔除测试 |
| `void bindShaderData(const ShaderVar&) const` | 将相机数据绑定到着色器变量 |
| `Changes beginFrame(bool firstFrame)` | 帧开始处理，返回变更标志 |
| `void updateFromAnimation(const float4x4&) override` | 从动画变换更新相机 |
| `void renderUI(Gui::Widgets&)` | 渲染 UI 控件 |

### `Camera::Changes` 枚举

| 值 | 说明 |
|----|------|
| `None` | 无变更 |
| `Movement` | 位置/朝向变更 |
| `Exposure` | 曝光参数变更 |
| `FocalDistance` | 焦点距离变更 |
| `Jitter` | 抖动变更 |
| `Frustum` | 视锥体参数变更 |
| `Aperture` | 光圈变更 |
| `History` | 前帧矩阵变更（运动向量相关） |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mData` | `CameraData` | 当前帧相机数据 |
| `mPrevData` | `CameraData` | 前帧相机数据 |
| `mFrustumPlanes[6]` | 结构体数组 | 视锥体平面（用于剔除） |
| `mJitterPattern` | 结构体 | 抖动模式生成器和缩放因子 |
| `mDirty` | `bool` | 脏标志，标记是否需要重新计算矩阵 |
| `kDefaultFrameHeight` | `float` | 默认胶片高度 24mm（35mm 全画幅） |

## 依赖关系

### 本文件引用

- `CameraData.slang` — 相机数据结构（CPU/GPU 共享）
- `Scene/Animation/Animatable.h` — 可动画对象基类
- `Core/Program/ShaderVar.h` — 着色器变量绑定
- `Utils/Math/FalcorMath.h` — 焦距与 FOV 转换
- `Utils/SampleGenerators/CPUSampleGenerator.h` — 抖动模式生成
- `Utils/Scripting/ScriptBindings.h` — Python 绑定

### 被以下文件引用

- `CameraController.h` — 相机控制器
- `Scene/Scene.h` — 场景管理
- `Camera.slang` — GPU 端相机结构

## 实现细节

- 矩阵计算采用惰性求值：仅在 `mDirty` 为 true 时重新计算
- 焦距为 0 时使用正交投影，视口大小由观察距离确定
- 抖动矩阵通过平移变换叠加到投影矩阵上
- 视锥体平面从视图投影矩阵的转置中提取
- 光线追踪相关的 `cameraU/V/W` 向量根据 FOV 和焦距缩放
- 前帧数据用于计算运动向量，在 `beginFrame` 中交换
- 支持持久视图/投影矩阵模式，绕过自动计算
- Python 绑定暴露大量属性（position、target、up、focalLength 等）
