# Visualization2d.ps.slang 着色器文档

> 路径: `Source/Samples/Visualization2D/Visualization2d.ps.slang`
> 类型: Slang 像素着色器
> 模块: Samples/Visualization2D

## 功能概述

标记演示(Marker Demo)场景的像素着色器。该着色器使用 SDF(有符号距离场)2D 图元库绘制各种 2D 标记形状(圆、方、菱形、心形、箭头等),展示 SDF 2D 绘图工具库的功能。支持鼠标交互绘制向量和线段,以及通过 SDF 布尔运算(平滑并集、平滑差集)实现动态形状混合效果。

## 结构体与接口

### 常量缓冲区 `Visual2DCB`

| 成员 | 类型 | 说明 |
|------|------|------|
| `iResolution` | `float2` | 渲染目标分辨率 |
| `iMousePosition` | `float2` | 鼠标位置(屏幕坐标) |
| `iGlobalTime` | `float` | 全局时间(秒) |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float4 main(float2 texC: TEXCOORD) : SV_TARGET` | 像素着色器入口,绘制所有 SDF 2D 图元和交互元素 |

## 预定义常量

| 常量 | 类型 | 说明 |
|------|------|------|
| `side` | `float` | 图元尺寸参考值(0.1) |
| `gray/red/cyan/green/greenAlpha/blue/blueAlpha/orange/black` | `float4` | 预定义颜色 |

## 依赖关系 / import

| 模块 | 说明 |
|------|------|
| `Utils.SDF.SDF2DDraw` | SDF 2D 绘图函数(`sdfDraw`, `sdfFilled` 等) |
| `Utils.SDF.SDF2DPrimitives` | SDF 2D 基本图元(圆、方、心形、箭头、向量、圆角矩形、三角形等) |
| `Utils.SDF.SDFOperations` | SDF 布尔运算(平滑并集、平滑差集) |

## 实现细节

- **图元展示**: 在屏幕上排列展示各种 SDF 2D 图元,包括:
  - 圆(`SDF2DCircle`)、方(`SDF2DSquare`)、菱形(`SDF2DDiamond`)
  - 心形(`SDF2DHeart`,带旋转动画)、V 形(`SDF2DChevron`)、环(`SDF2DRing`)
  - 标签(`SDF2DTag`,带旋转动画)、十字(`SDF2DCross`)、星号(`SDF2DAsterisk`)
  - 无穷符号(`SDF2DInfinity`)、定位标记(`SDF2DPin`)、箭头(`SDF2DArrow`)
- **鼠标交互**: 在鼠标位置绘制向量(`SDF2DVector`)、圆角线段(`SDF2DRoundedLine`)和圆点。
- **圆角矩形**: 展示了带不同圆角半径和旋转角度的圆角矩形(`SDF2DRoundedBox`)。
- **动画三角形**: 使用正弦函数驱动顶点位置实现三角形的动态变形。
- **SDF 布尔运算**: 使用两个圆的平滑并集(`sdfSmoothUnion`)减去一个小圆的平滑差集(`sdfSmoothSubtraction`)创建动态形状,展示 SDF 的组合能力。
- 坐标系:X 向右,Y 向上,归一化到 `iResolution.x` 以保持宽高比。
