# AnimationController 源码文档

> 路径: `Source/Falcor/Scene/Animation/AnimationController.h` + `.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Animation

## 功能概述

场景动画系统的核心控制器，负责协调所有动画子系统：场景图节点动画、骨骼蒙皮和顶点缓存动画。每帧计算局部变换矩阵、传播世界变换、上传到 GPU 并执行蒙皮计算着色器。

## 类与接口

### `AnimationController`

- **职责**: 管理场景的完整动画流水线，包括节点动画、蒙皮和顶点缓存

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `AnimationController(ref<Device>, Scene*, const SkinningVertexVector&, uint32_t, const vector<ref<Animation>>&)` | 构造函数，创建 GPU 资源和蒙皮通道 |
| `void addAnimatedVertexCaches(vector<CachedCurve>&&, vector<CachedMesh>&&)` | 添加顶点缓存动画 |
| `bool animate(RenderContext*, double currentTime)` | 执行一帧动画，返回是否有变更 |
| `bool hasAnimations() const` | 是否有任何动画 |
| `bool hasSkinnedMeshes() const` | 是否有蒙皮网格 |
| `bool hasAnimatedVertexCaches() const` | 是否有顶点缓存动画 |
| `void setEnabled(bool)` | 启用/禁用动画系统 |
| `void setIsLooped(bool)` | 设置全局循环 |
| `void setNodeEdited(size_t nodeID)` | 标记节点被外部编辑 |
| `bool isMatrixChanged(NodeID) const` | 检查矩阵是否变更 |
| `const vector<float4x4>& getLocalMatrices() const` | 获取局部矩阵 |
| `const vector<float4x4>& getGlobalMatrices() const` | 获取世界矩阵 |
| `const vector<float4x4>& getInvTransposeGlobalMatrices() const` | 获取逆转置世界矩阵 |
| `ref<Buffer> getPrevVertexData() const` | 获取前帧顶点数据缓冲区 |
| `uint64_t getMemoryUsageInBytes() const` | 获取总 GPU 内存使用量 |
| `void renderUI(Gui::Widgets&)` | 渲染 UI |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mAnimations` | `vector<ref<Animation>>` | 动画列表 |
| `mLocalMatrices` | `vector<float4x4>` | 局部变换矩阵（每节点） |
| `mGlobalMatrices` | `vector<float4x4>` | 世界变换矩阵 |
| `mInvTransposeGlobalMatrices` | `vector<float4x4>` | 逆转置世界矩阵（法线变换用） |
| `mMatricesChanged` | `vector<bool>` | 每矩阵的变更标志 |
| `mpSkinningPass` | `ref<ComputePass>` | 蒙皮计算着色器 |
| `mSkinningMatrices` | `vector<float4x4>` | 蒙皮矩阵 |
| `mpVertexCache` | `unique_ptr<AnimatedVertexCache>` | 顶点缓存动画管理器 |
| `mpWorldMatricesBuffer` | `ref<Buffer>` | GPU 世界矩阵缓冲区 |
| `mpPrevWorldMatricesBuffer` | `ref<Buffer>` | GPU 前帧世界矩阵缓冲区 |
| `mpPrevVertexData` | `ref<Buffer>` | 前帧顶点数据（蒙皮+顶点动画共享） |

## 依赖关系

### 本文件引用

- `Animation.h` — 动画类
- `AnimatedVertexCache.h` — 顶点缓存动画
- `Core/API/Buffer.h` — GPU 缓冲区
- `Core/Pass/ComputePass.h` — 计算着色器通道
- `Scene/Scene.h` — 场景数据
- `Scene/SceneTypes.slang` — 场景类型定义
- `Utils/SplitBuffer.h` — 分块缓冲区

### 被以下文件引用

- `Scene/Scene.h` — 场景持有动画控制器

## 实现细节

- **初始化流程**: 创建世界矩阵 GPU 缓冲区 -> 初始化前帧顶点数据 -> 创建蒙皮计算通道
- **每帧更新流程**:
  1. 检查外部编辑的节点
  2. 检查启用/禁用状态变化（首次或切换时执行完整初始化）
  3. 更新局部矩阵（驱动每个 Animation）
  4. 传播世界矩阵（父->子，仅更新变更的节点）
  5. 上传到 GPU（仅上传连续变更区段，优化带宽）
  6. 交换前帧/当前帧缓冲区
  7. 执行蒙皮计算着色器
  8. 驱动顶点缓存动画
- **蒙皮**: 蒙皮矩阵 = 逆世界矩阵 * 骨骼矩阵 * 绑定矩阵，转换回网格局部空间
- **矩阵上传优化**: 检测连续变更的矩阵区段，批量上传而非逐个上传
- **前帧数据管理**: 前帧世界矩阵通过交换缓冲区指针实现，避免数据拷贝
- **缓冲区共享**: 蒙皮网格和顶点动画网格共享 `mpPrevVertexData` 缓冲区
