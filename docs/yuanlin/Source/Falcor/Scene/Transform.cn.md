# Transform 源码文档

> 路径: `Source/Falcor/Scene/Transform.h` + `Source/Falcor/Scene/Transform.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene

## 功能概述

Transform 是一个变换辅助类，基于平移、旋转和缩放三个分量构建变换矩阵。支持多种组合顺序（默认为 缩放->旋转->平移），并提供欧拉角与四元数之间的转换。

## 类与接口

### `Transform`

- **继承**: 无
- **职责**: 管理 TRS（平移-旋转-缩放）变换，按需生成变换矩阵

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `const float3& getTranslation() const` | 获取平移分量 |
| `void setTranslation(const float3&)` | 设置平移分量 |
| `const float3& getScaling() const` | 获取缩放分量 |
| `void setScaling(const float3&)` | 设置缩放分量 |
| `const quatf& getRotation() const` | 获取旋转四元数 |
| `void setRotation(const quatf&)` | 设置旋转四元数 |
| `float3 getRotationEuler() const` | 获取欧拉角（弧度） |
| `void setRotationEuler(const float3&)` | 设置欧拉角（弧度） |
| `float3 getRotationEulerDeg() const` | 获取欧拉角（度） |
| `void setRotationEulerDeg(const float3&)` | 设置欧拉角（度） |
| `void lookAt(const float3& position, const float3& target, const float3& up)` | 设置 LookAt 变换 |
| `const float4x4& getMatrix() const` | 获取变换矩阵（延迟计算） |

#### `CompositionOrder` 枚举

| 值 | 说明 |
|---|------|
| `ScaleRotateTranslate` | 缩放->旋转->平移（默认） |
| `ScaleTranslateRotate` | 缩放->平移->旋转 |
| `RotateScaleTranslate` | 旋转->缩放->平移 |
| 其他... | 六种全排列 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mTranslation` | `float3` | 平移 (0,0,0) |
| `mScaling` | `float3` | 缩放 (1,1,1) |
| `mRotation` | `quatf` | 旋转四元数（单位四元数） |
| `mDirty` | `mutable bool` | 脏标志，矩阵需要重算 |
| `mMatrix` | `mutable float4x4` | 缓存的变换矩阵 |

## 依赖关系

### 本文件引用

- `Core/Macros.h` - 基础宏
- `Utils/Math/Vector.h` - 向量类型
- `Utils/Math/Matrix.h` - 矩阵类型
- `Utils/Math/Quaternion.h` - 四元数类型

### 被以下文件引用

- `SceneBuilder.h` - 场景图节点变换
- `SceneCache.h` - 变换序列化
- `TriangleMesh.h` - 应用变换到网格
- `Animation/Animation.h` - 动画关键帧

## 实现细节

- 使用延迟计算（Lazy Evaluation）：仅在 `getMatrix()` 调用且 `mDirty == true` 时重新计算矩阵
- `SceneCache` 被声明为友元类以直接访问私有成员进行序列化
