# Camera.slang 源码文档

> 路径: `Source/Falcor/Scene/Camera/Camera.slang`
> 类型: Slang 着色器文件
> 模块: Scene/Camera

## 功能概述

定义 GPU 端的相机结构体，提供针孔相机和薄透镜相机模型的光线计算方法。用于光线追踪渲染中的主光线生成。

## 结构体与接口

### `Camera`

| 成员 | 类型 | 说明 |
|------|------|------|
| `data` | `CameraData` | 相机参数数据 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float3 getPosition()` | 获取相机世界空间位置 |
| `float4x4 getViewProj()` | 获取视图投影矩阵 |
| `Ray computeRayPinhole(uint2 pixel, uint2 frameDim, bool applyJitter = true)` | 计算针孔相机射线，考虑抖动偏移 |
| `float3 computeNonNormalizedRayDirPinhole(uint2 pixel, uint2 frameDim, bool applyJitter = true)` | 计算未归一化的针孔相机射线方向 |
| `Ray computeRayThinlens(uint2 pixel, uint2 frameDim, float2 u)` | 计算薄透镜相机射线（支持景深效果） |

### 针孔相机射线计算

1. 将像素坐标转换为 [0,1] 屏幕空间坐标
2. 应用抖动偏移
3. 转换为 [-1,1] NDC 坐标
4. 使用 `cameraU`、`cameraV`、`cameraW` 基向量合成射线方向
5. 根据近远平面设置 `tMin` 和 `tMax`

### 薄透镜相机射线计算

1. 在针孔射线基础上，从光圈采样偏移原点
2. 使用均匀圆盘采样 `sample_disk(u)` 生成光圈上的采样点
3. 射线目标保持不变，重新计算方向以模拟景深效果

## 依赖关系

### import

- `Scene.Camera.CameraData` — 相机数据结构
- `Utils.Math.Ray`（导出） — 射线结构
- `Utils.Math.MathHelpers` — 数学辅助函数（`sample_disk` 等）
