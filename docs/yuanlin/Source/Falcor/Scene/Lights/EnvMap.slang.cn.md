# EnvMap.slang 源码文档

> 路径: `Source/Falcor/Scene/Lights/EnvMap.slang`
> 类型: Slang 着色器文件
> 模块: Scene/Lights

## 功能概述

GPU 端的环境贴图访问结构体，提供方向到 UV 的转换、纹理采样和辐照度评估功能。支持世界空间到局部空间的方向变换，以及经纬度映射的坐标转换。

## 结构体与接口

### `EnvMap`

| 成员 | 类型 | 说明 |
|------|------|------|
| `envMap` | `Texture2D` | 环境贴图纹理 |
| `envSampler` | `SamplerState` | 纹理采样器 |
| `data` | `EnvMapData` | 环境贴图参数数据 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `uint2 getDimensions()` | 获取纹理尺寸 |
| `float3 evalTexel(uint2 coord)` | 按纹素坐标评估辐照度 |
| `float3 eval(float2 uv, float lod = 0)` | 按 UV 坐标评估辐照度 |
| `float3 eval(float3 dir, float lod = 0)` | 按世界空间方向评估辐照度 |
| `float2 localToUv(float3 dir)` | 局部空间方向转 UV（经纬度映射） |
| `float3 uvToLocal(float2 uv)` | UV 转局部空间方向 |
| `float2 worldToUv(float3 dir)` | 世界空间方向转 UV |
| `float3 uvToWorld(float2 uv)` | UV 转世界空间方向 |
| `float3 toWorld(float3 dir)` | 局部空间转世界空间方向 |
| `float3 toLocal(float3 dir)` | 世界空间转局部空间方向 |
| `float3 getIntensity()` | 获取强度缩放因子（含色调） |

## 依赖关系 / import

- `Scene.Lights.EnvMapData` — 数据结构定义
- `Utils.Math.MathHelpers` — 经纬度映射工具函数（`world_to_latlong_map`, `latlong_map_to_world`）
