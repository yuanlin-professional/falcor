# SelectionWheel 源码文档

> 路径: `Source/RenderPasses/SDFEditor/SelectionWheel.h` + `SelectionWheel.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/SDFEditor

## 功能概述

环形选择轮 UI 组件，用于 SDF 编辑器中选择形状类型和操作类型。通过 2D SDF 圆弧扇区实现，支持分组、扇区高亮和鼠标悬停检测。

## 类与接口

### `SelectionWheel`

- **继承**: 无
- **命名空间**: `Falcor`
- **职责**: 管理环形选择轮的渲染和交互逻辑

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `SelectionWheel(Marker2DSet&)` | 构造函数，引用一个标记集 |
| `void update(const float2&, const Desc&)` | 更新选择轮状态并渲染到标记集 |
| `bool isMouseOnSector(float2&, uint32_t, uint32_t)` | 检查鼠标是否在指定扇区上 |
| `bool isMouseOnGroup(float2&, uint32_t, uint32_t&)` | 检查鼠标是否在指定分组上，返回扇区索引 |
| `float2 getCenterPositionOfSector(uint32_t, uint32_t)` | 获取扇区中心位置 |
| `float getAngleOfSectorInGroup(uint32_t)` | 获取指定分组中每个扇区的角度 |
| `float getRotationOfSector(uint32_t, uint32_t)` | 获取指定扇区的起始旋转角度 |
| `float getGroupAngle()` | 获取每个分组的角度 |

#### `Desc` 结构体

| 成员 | 类型 | 说明 |
|------|------|------|
| `sectorGroups` | `vector<uint32_t>` | 每个分组的扇区数量 |
| `position` | `float2` | 选择轮中心位置 |
| `minRadius` / `maxRadius` | `float` | 内/外半径 |
| `baseColor` | `float4` | 基础颜色 |
| `highlightColor` | `float4` | 高亮颜色 |
| `lineColor` | `float4` | 分隔线颜色 |
| `borderWidth` | `float` | 边框宽度 |

## 依赖关系

### 本文件引用

- `Falcor.h`
- `Marker2DSet.h`

### 被以下文件引用

- `SDFEditor.h`

## 实现细节

- 选择轮通过圆弧扇区（`addCircleSector`）绘制，分组之间有小间距
- 鼠标悬停时高亮对应扇区，其他扇区使用基础颜色
- 角度计算从正上方（PI/2 偏移）开始，逆时针排列
- 支持边缘裁切（`margin`）以在分组之间创建视觉间隔
