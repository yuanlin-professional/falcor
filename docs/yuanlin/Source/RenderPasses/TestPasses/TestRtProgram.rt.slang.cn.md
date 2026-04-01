# TestRtProgram.rt.slang 源码文档

> 路径: `Source/RenderPasses/TestPasses/TestRtProgram.rt.slang`
> 类型: 光线追踪着色器
> 模块: RenderPasses/TestPasses

## 功能概述

此着色器实现了 TestRtProgram 渲染通道的所有光线追踪着色器入口点，包括光线生成、miss、最近命中、任意命中和交叉着色器。支持两种测试模式：

- **模式 0**: 多光线类型 + 三角形/自定义图元混合追踪
- **模式 1**: 使用 `createDynamicObject` 的类型特化命中组

## 结构体与接口

### `TestProgram`（参数块）

| 字段 | 类型 | 说明 |
|------|------|------|
| `frameDim` | `uint2` | 帧尺寸 |
| `output` | `RWTexture2D<float4>` | 输出纹理 |

### `RayData`（光线载荷）

| 字段 | 类型 | 说明 |
|------|------|------|
| `t` | `float` | 命中距离 |
| `color` | `float3` | 命中颜色 |

### `MyIntersectionAttribs`（自定义交叉属性，仅模式 0）

| 字段 | 类型 | 说明 |
|------|------|------|
| `t` | `float` | 交叉点距离 |

### 接口 `IMtl`（仅模式 1）

| 方法 | 说明 |
|------|------|
| `float3 getColor()` | 返回材质颜色 |
| `bool alphaTest(uint2 pixel)` | 执行基于像素位置的 Alpha 测试 |

### `Mtl0` / `Mtl1` / `Mtl2`（实现 `IMtl`，仅模式 1）

| 类型 | 颜色 | Alpha 测试模式 |
|------|------|---------------|
| `Mtl0` | 红色 (1,0,0) | `pixel.x % 8 == 0` |
| `Mtl1` | 绿色 (0,1,0) | `pixel.y % 8 == 0` |
| `Mtl2` | 蓝色 (0,0,1) | `(pixel.x + pixel.y) % 8 == 0` |

## 函数

### 光线生成着色器

| 函数 | 说明 |
|------|------|
| `void rayGen()` | 使用针孔相机生成光线，调用 `TestProgram.execute()` |

### Miss 着色器

| 函数 | 说明 |
|------|------|
| `void miss0(inout RayData)` | 输出暗灰色 (0.05) |
| `void miss1(inout RayData)` | 输出浅灰色 (0.1) |

### 模式 0 命中着色器（三角形）

| 函数 | 说明 |
|------|------|
| `void anyHit(...)` | 使用场景材质系统执行 Alpha 测试 |
| `void closestHitMtl0(...)` | 灰色 (0.5) |
| `void closestHitMtl1(...)` | 蓝色 (0,0,1) |
| `void closestHitRed(...)` | 红色 (1,0,0) |
| `void closestHitGreen(...)` | 绿色 (0,1,0) |

### 模式 0 命中着色器（球体自定义图元）

| 函数 | 说明 |
|------|------|
| `void intersectSphere()` | 球体交叉测试（使用 AABB 内切球） |
| `void closestHitSphereMtl0(...)` | 暗灰色 (0.25) |
| `void closestHitSphereMtl1(...)` | 紫色 (0.5, 0, 0.5) |
| `void closestHitSpherePurple(...)` | 亮紫色 (0.75, 0, 1) |
| `void closestHitSphereYellow(...)` | 黄色 (1, 1, 0) |

### 模式 1 命中着色器

| 函数 | 说明 |
|------|------|
| `void anyHit(...)` | 使用 `createDynamicObject<IMtl>` 动态选择材质并执行 Alpha 测试 |
| `void closestHit(...)` | 使用 `createDynamicObject<IMtl>` 动态选择材质并获取颜色 |

## 依赖关系 / import

- `Scene.Raytracing` — 提供场景光线追踪基础设施
- `Utils.Geometry.IntersectionHelpers` — 提供 `intersectRaySphere` 球体交叉测试

## 实现细节

1. **光线类型选择**: 模式 0 中使用像素坐标的对角线模式 `(x+y) & 0x8` 选择光线类型，miss 着色器通过棋盘格模式 `(x^y) & 0x10` 选择。
2. **球体交叉**: 从 AABB 计算内切球半径（三轴最小值的一半），使用解析射线-球体交叉测试。
3. **动态对象创建**: 模式 1 使用 `createDynamicObject<IMtl, int>(type, dummyData)` 根据几何体 ID 动态创建不同材质类型实例，测试 Slang 的动态分发机制。
