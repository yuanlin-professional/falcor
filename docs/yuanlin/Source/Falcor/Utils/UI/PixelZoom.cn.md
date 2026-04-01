# PixelZoom 源码文档

> 路径: `Source/Falcor/Utils/UI/PixelZoom.h` + `Source/Falcor/Utils/UI/PixelZoom.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/UI

## 功能概述

PixelZoom 类实现了一个像素级放大镜功能，用于在渲染输出中放大鼠标周围的区域以辅助细节检查。按住 Z 键激活放大模式，鼠标滚轮可调整放大倍数。放大后的图像以点采样（最近邻）方式放大并叠加显示在鼠标位置周围。

## 类与接口

### `PixelZoom`

- **继承**: 无
- **职责**: 提供像素级屏幕放大功能，通过 Blit 操作实现区域放大

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `PixelZoom(ref<Device> pDevice, const Fbo* pBackbuffer)` | 构造函数，初始化设备引用并创建 Blit 用 FBO |
| `void render(RenderContext* pCtx, Fbo* backBuffer)` | 执行放大渲染：将鼠标周围区域从后缓冲复制到临时 FBO，放大后 Blit 回后缓冲 |
| `bool onMouseEvent(const MouseEvent& me)` | 处理鼠标事件：记录鼠标位置，滚轮调整放大区域大小 |
| `bool onKeyboardEvent(const KeyboardEvent& ke)` | 处理键盘事件：Z 键按下/释放切换放大模式 |
| `void onResize(const Fbo* pBackbuffer)` | 窗口大小变化时重新创建源 Blit FBO |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | GPU 设备引用 |
| `mSrcZoomSize` | `int32_t` | 源采样区域半径（像素），默认 5，可通过滚轮调节 |
| `mDstZoomSize` | `const uint32_t` | 放大后显示区域大小，固定 200 像素 |
| `mZoomCoefficient` | `const uint32_t` | 滚轮缩放系数，固定为 4 |
| `mpSrcBlitFbo` | `ref<Fbo>` | 源 Blit FBO，与后缓冲同尺寸 |
| `mpDstBlitFbo` | `ref<Fbo>` | 目标 Blit FBO，固定 200x200 像素 |
| `mMousePos` | `float2` | 鼠标归一化位置 |
| `mShouldZoom` | `bool` | 是否处于放大模式 |

### 辅助函数（匿名命名空间）

| 函数签名 | 说明 |
|----------|------|
| `void clampToEdge(float2& pix, uint32_t width, uint32_t height, uint32_t offset)` | 将像素坐标限制在屏幕边缘内，防止采样超出边界 |

## 依赖关系

### 本文件引用

- `Core/API/FBO.h` — 帧缓冲对象
- `Core/API/RenderContext.h` — `blit` 和 `copyResource` 操作
- `Utils/Math/Vector.h` — 向量数学类型
- `Utils/UI/InputTypes.h` — 输入事件类型和按键枚举

### 被以下文件引用

- Falcor 应用程序框架（SampleApp 等）用于提供调试时的像素放大功能

## 实现细节

- 放大流程分三步：
  1. 将后缓冲完整复制到 `mpSrcBlitFbo`
  2. 将 `mpSrcBlitFbo` 中鼠标周围的小区域 Blit 到 `mpDstBlitFbo`（200x200），实现放大
  3. 将 `mpDstBlitFbo` Blit 回后缓冲的鼠标位置周围
- 使用 `TextureFilteringMode::Point`（点采样/最近邻）确保像素边界清晰可见
- 滚轮向上滚动缩小采样区域（放大倍数增加），向下滚动扩大采样区域（放大倍数减小）
- `mSrcZoomSize` 最小值限制为 3，防止采样区域过小
- 通过 `clampToEdge` 确保放大区域不会超出屏幕边界
- Z 键通过 KeyPressed/KeyReleased 切换，松开即退出放大模式
- `onMouseEvent` 在放大模式下会消耗非 Move 类型的鼠标事件，但允许 Move 事件继续传播
