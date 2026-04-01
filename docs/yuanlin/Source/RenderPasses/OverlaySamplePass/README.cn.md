# OverlaySamplePass -- 叠加采样可视化

## 功能概述

OverlaySamplePass 是一个演示性渲染通道，展示如何在渲染通道中使用 `renderOverlayUI` 回调直接通过 ImGui 在屏幕上绘制简单的几何图元。该通道将输入缓冲区直接拷贝到输出缓冲区，然后在其上叠加绘制一系列 2D 图元用于可视化演示。

### 核心功能

- **输入/输出透传**：将输入的 RGBA32Float 缓冲区通过 blit 操作拷贝到输出缓冲区
- **ImGui 叠加绘制**：利用 `ImGui::GetBackgroundDrawList()` 在后台绘图列表上绘制各类图元
- **图元展示网格**：在屏幕上创建 5x3 的矩形网格，每个单元格绘制不同类型的 2D 图元
- **支持的图元类型**（共 15 种）：
  - 直线（Line）
  - 矩形/填充矩形（Rect / RectFilled）
  - 多色填充矩形（RectFilledMultiColor）
  - 四边形/填充四边形（Quad / QuadFilled）-- 菱形示例
  - 三角形/填充三角形（Triangle / TriangleFilled）
  - 圆形/填充圆形（Circle / CircleFilled）
  - 正多边形/填充正多边形（Ngon / NgonFilled）
  - 文本（Text）
  - 折线/星形多段线（Polyline）
  - 凸多边形填充（ConvexPolyFilled）
  - 贝塞尔曲线（BezierCubic）

## 文件清单

| 文件 | 类型 | 说明 |
|------|------|------|
| `OverlaySamplePass.h` | C++ 头文件 | 类声明，继承自 `RenderPass` |
| `OverlaySamplePass.cpp` | C++ 源文件 | 插件注册、执行逻辑与 ImGui 叠加绘制实现 |
| `CMakeLists.txt` | 构建配置 | CMake 插件构建定义 |

## 依赖关系

| 依赖项 | 说明 |
|--------|------|
| `Falcor.h` | Falcor 核心框架 |
| `RenderGraph/RenderPass.h` | 渲染通道基类 |
| `RenderGraph/RenderPassHelpers.h` | 渲染通道辅助工具（通道列表定义） |
| `RenderGraph/RenderPassStandardFlags.h` | 标准渲染通道标志位 |
| `imgui.h` | ImGui 即时模式 GUI 库（用于叠加绘制） |
| `pybind11` | Python 绑定支持（预留接口） |

## 关键类与接口

### `OverlaySamplePass` (继承自 `RenderPass`)

```
FALCOR_PLUGIN_CLASS(OverlaySamplePass, "OverlaySamplePass", "...")
```

**公有方法：**

| 方法 | 说明 |
|------|------|
| `create(ref<Device>, const Properties&)` | 静态工厂方法，创建通道实例 |
| `getProperties() const` | 返回通道属性（当前为空） |
| `reflect(const CompileData&)` | 声明输入/输出通道：`input`(RGBA32Float, 可选) / `output`(RGBA32Float) |
| `execute(RenderContext*, const RenderData&)` | 执行渲染：将 input blit 到 output，更新帧尺寸与帧计数 |
| `renderOverlayUI(RenderContext*)` | 叠加 UI 绘制回调：在屏幕上绘制 5x3 图元网格 |
| `registerBindings(pybind11::module&)` | Python 绑定注册（当前为空实现） |

**私有成员：**

| 成员 | 类型 | 说明 |
|------|------|------|
| `mFrameDim` | `uint2` | 帧缓冲尺寸 |
| `mFrameCount` | `uint32_t` | 累计帧计数 |
