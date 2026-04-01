# Animation 源码文档

> 路径: `Source/Falcor/Scene/Animation/Animation.h` + `.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Animation

## 功能概述

实现场景节点的关键帧动画。每个 Animation 对象关联一个场景图节点，包含一系列关键帧（位置、旋转、缩放），支持线性和 Hermite 两种插值模式。提供多种边界行为（Constant、Linear、Cycle、Oscillate）用于控制关键帧范围外的动画表现。

## 类与接口

### `Animation`

- **继承**: `Object`
- **职责**: 存储和计算场景节点的关键帧动画变换

#### 枚举类型

**`InterpolationMode`**
| 值 | 说明 |
|----|------|
| `Linear` | 线性插值 |
| `Hermite` | Hermite 样条插值（贝塞尔形式） |

**`Behavior`**
| 值 | 说明 |
|----|------|
| `Constant` | 保持边界关键帧值 |
| `Linear` | 线性外推 |
| `Cycle` | 循环 |
| `Oscillate` | 往复振荡 |

**`Keyframe` 结构体**
| 成员 | 类型 | 说明 |
|------|------|------|
| `time` | `double` | 时间戳 |
| `translation` | `float3` | 平移 |
| `scaling` | `float3` | 缩放 |
| `rotation` | `quatf` | 旋转四元数 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<Animation> create(string_view, NodeID, double)` | 工厂方法 |
| `float4x4 animate(double currentTime)` | 计算指定时间的变换矩阵 |
| `void addKeyframe(const Keyframe&)` | 添加关键帧（按时间排序） |
| `const Keyframe& getKeyframe(double time) const` | 获取指定时间的关键帧 |
| `fstd::span<const Keyframe> getKeyframes() const` | 获取所有关键帧 |
| `bool doesKeyframeExists(double time) const` | 检查关键帧是否存在 |
| `void setInterpolationMode(InterpolationMode)` | 设置插值模式 |
| `void setPreInfinityBehavior(Behavior)` | 设置前边界行为 |
| `void setPostInfinityBehavior(Behavior)` | 设置后边界行为 |
| `void setEnableWarping(bool)` | 启用/禁用首尾关键帧连接 |
| `void renderUI(Gui::Widgets&)` | 渲染 UI |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mName` | `string` | 动画名称 |
| `mNodeID` | `NodeID` | 关联的场景图节点 ID |
| `mDuration` | `double` | 动画总时长（秒） |
| `mKeyframes` | `vector<Keyframe>` | 关键帧列表（按时间排序） |
| `mCachedFrameIndex` | `size_t` | 缓存的帧索引（加速查找） |

## 依赖关系

### 本文件引用

- `Core/Object.h` — 基础对象类
- `Scene/SceneIDs.h` — NodeID 类型
- `Utils/Math/Quaternion.h` — 四元数
- `Scene/Transform.h` — 变换类（Python 绑定中使用）
- `Utils/Scripting/ScriptBindings.h` — Python 绑定

### 被以下文件引用

- `AnimationController.h` — 动画控制器管理动画列表
- `AnimatedVertexCache.h` — 顶点缓存动画引用 Behavior 枚举

## 实现细节

- `animate()` 方法生成 T * R * S 组合变换矩阵
- Hermite 插值使用贝塞尔形式，需要至少 4 个关键帧；不足时回退为线性插值
- 旋转使用 slerp（球面线性插值），Hermite 模式下使用贝塞尔 slerp
- 线性外推支持 t < 0 或 t > 1 的范围
- `calcSampleTime` 处理关键帧范围外的时间映射（Cycle 使用 fmod，Oscillate 使用乒乓映射）
- warping 模式启用时，最后一帧与第一帧之间可以插值（环形动画）
- 关键帧查找使用缓存索引优化，避免每次从头搜索
- Python 绑定暴露了所有关键属性和 `addKeyframe` 方法
