# Marker2DTypes.slang 源码文档

> 路径: `Source/RenderPasses/SDFEditor/Marker2DTypes.slang`
> 类型: Slang 共享类型定义
> 模块: RenderPasses/SDFEditor

## 功能概述

定义 SDF 编辑器 2D GUI 标记系统的所有类型，包括 2D 形状枚举、标记数据结构体（blob、变换、各种形状专用数据）、边框排除标记等。这些类型在 CPU 和 GPU 之间共享。

## 结构体与接口

### `SDF2DShapeType` (枚举)

2D 形状类型：`Circle`、`Square`、`Diamond`、`Heart`、`Chevron`、`Ring`、`Tag`、`Cross`、`Asterisk`、`Infinity`、`Pin`、`Arrow`、`RoundedBox`、`Triangle`、`RoundedLine`、`Vector`、`MarkerOpMarker`、`ArrowFromTwoTris`、`CircleSector`。

### `ExcludeBorderFlags` (枚举)

边框排除标记：`None`(0)、`Top`(1)、`Right`(2)、`Bottom`(4)、`Left`(8)。

### 数据结构体

| 结构体 | 说明 |
|--------|------|
| `Marker2DPayload` | 标记负载数据，固定 20 个 uint |
| `Marker2DDataBlob` | 标记数据 blob，包含类型和负载 |
| `Marker2DTransform` | 2D 变换（缩放、旋转、平移） |
| `SimpleMarker2DData` | 简单标记数据（变换 + 颜色） |
| `Marker2DRoundedLine` | 圆角线数据（起点、终点、宽度） |
| `RoundedLineMarker2DData` | 圆角线标记 |
| `VectorMarker2DData` | 向量标记（线 + 箭头高度） |
| `TriangleMarker2DData` | 三角形标记（三个顶点 + 颜色） |
| `RoundedBoxMarker2DData` | 圆角矩形标记 |
| `BasicMarker2D` | 基本标记（位置、类型、大小） |
| `MarkerOpMarker2DData` | 操作标记（操作类型 + 两个基本标记） |
| `ArrowFromTwoTrisMarker2DData` | 双三角形箭头标记 |
| `CircleSectorMarker2DData` | 圆弧扇区标记（位置、旋转、角度、半径范围、边框） |

## 依赖关系 / import

- `Utils/HostDeviceShared.slangh` — CPU/GPU 共享宏
- `Utils.SDF.SDFOperationType` — SDF 操作类型枚举
