# SpectrumUI 源码文档

> 路径: `Source/Falcor/Utils/UI/SpectrumUI.h` + `Source/Falcor/Utils/UI/SpectrumUI.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/UI

## 功能概述

SpectrumUI 是一个基于 ImGui 的光谱可视化与编辑组件。它能够绘制 `SampledSpectrum<T>` 光谱曲线，支持交互式编辑光谱采样点、显示 CIE 1931 颜色匹配函数、光谱色带、以及将光谱转换为 RGB 颜色预览。支持 `float` 和 `float3` 两种光谱数据类型，后者可同时显示三条独立曲线。

## 类与接口

### `SpectrumUI<T>`

- **继承**: 无
- **职责**: 提供 `SampledSpectrum<T>` 的交互式可视化和编辑界面

#### 模板参数

- `T`: 光谱数据类型，支持 `float`（单通道）和 `float3`（三通道 RGB）

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `SpectrumUI()` | 默认构造，使用默认波长范围 [350, 750] nm 和强度范围 [0, 1] |
| `SpectrumUI(const float2& wavelengthRange, const float2& spectralIntensityRange)` | 自定义范围构造 |
| `void setWavelengthRange(const float2& range)` | 设置波长显示范围 |
| `void setSpectralIntensityRange(const float2& range)` | 设置光谱强度显示范围 |
| `bool render(Gui::Widgets& w, const std::string name, std::vector<SampledSpectrum<T>*> spectra, bool renderOnlySpectrum)` | 渲染光谱 UI，返回是否有修改 |

#### 内部绘制方法

| 方法签名 | 说明 |
|----------|------|
| `drawSpectrumCurve(...)` | 绘制光谱曲线和采样点 |
| `drawSpectrumBar(...)` | 绘制 x 轴上的光谱色带 |
| `drawColorMatchingFunctions(...)` | 绘制 CIE 1931 XYZ 颜色匹配函数 |
| `drawTextWavelengthsAndTicks(...)` | 绘制 x 轴刻度和波长标签 |
| `drawTextSpectralIntensityAndTicks(...)` | 绘制 y 轴刻度和强度标签 |
| `handleMouse(...)` | 处理鼠标交互：拖拽采样点编辑光谱值 |
| `drawLine(...)` / `drawCircle(...)` | ImGui 绘制辅助函数 |
| `toXCoord(...)` / `toYCoord(...)` / `toCoords(...)` | 坐标转换（波长/强度 -> 画布坐标） |
| `generateNiceNumber(float x)` | 生成"美观"刻度数值（基于 Graphics Gems 算法） |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mWavelengthRange` | `float2` | 波长显示范围，默认 [350, 750] nm |
| `mSpectralIntensityRange` | `float2` | 光谱强度显示范围，默认 [0, 1] |
| `mEditSpectrumIndex` | `uint32_t` | 当前可编辑的曲线索引 |
| `mDrawSpectrumBar` | `bool` | 是否绘制光谱色带 |
| `mMultiplyWithSpectralIntensity` | `bool` | 色带是否乘以光谱强度 |
| `mDrawGridX` / `mDrawGridY` | `bool` | 是否绘制网格线 |
| `mDrawAreaHeight` | `uint32_t` | 绘制区域高度，默认 300 像素 |
| `mDrawColorMatchingFunctions` | `bool` | 是否绘制颜色匹配函数 |
| `mMovePoint` | `bool` | 是否正在拖拽采样点 |
| `mPointIndexToBeEdited` | `uint32_t` | 正在编辑的采样点索引 |
| `mInterpolationType` | `SpectrumInterpolation` | 插值类型（当前仅支持线性） |

### 便捷函数

| 函数签名 | 说明 |
|----------|------|
| `bool renderSpectrumUI(Gui::Widgets& w, SampledSpectrum<T>& spectrum, const char label[])` | 简化版：使用默认 UI 参数，不显示参数编辑界面 |
| `bool renderSpectrumUI(Gui::Widgets& w, SampledSpectrum<T>& spectrum, SpectrumUI<T>& spectrumUI, const char label[])` | 带 UI 参数的完整版 |
| `bool renderSpectrumUI(Gui::Widgets& w, std::vector<SampledSpectrum<T>*>& spectra, SpectrumUI<T>& spectrumUI, const char label[])` | 多光谱同时显示版 |

## 依赖关系

### 本文件引用

- `Core/Macros.h` — Falcor 宏
- `Core/Error.h` — 断言
- `Utils/Math/Vector.h` — 数学向量类型
- `Utils/Color/SampledSpectrum.h` — 采样光谱数据结构
- `Utils/Color/SpectrumUtils.h` — 光谱工具函数（波长转 RGB、颜色匹配函数）
- `Utils/Color/ColorHelpers.slang` — `sRGBToLinear` 颜色空间转换
- `Utils/UI/Gui.h` — `Gui::Widgets` 和 `Gui::Group` 控件
- `imgui.h` — ImGui 绘制 API

### 被以下文件引用

- `Utils/UI/Gui.cpp` — `Gui::Widgets::spectrum()` 方法委托给此模块
- 需要光谱编辑功能的渲染 Pass

## 实现细节

- 模板类显式实例化为 `SpectrumUI<float>` 和 `SpectrumUI<float3>`
- 对于 `float3` 类型，三个分量分别用红、绿、蓝颜色绘制曲线
- 鼠标交互逻辑：先搜索距离鼠标最近的采样点（在阈值半径 30 像素内），点击后进入拖拽模式
- 刻度生成算法基于 Paul Heckbert 的 "Nice Numbers for Graph Labels"（Graphics Gems, 1990）
- 光谱色带使用 `SpectrumUtils::wavelengthToRGB_Rec709` 将波长转为可见光颜色
- 颜色预览使用 D65 光源将光谱积分为 RGB
- UI 参数（波长范围、强度范围、网格显示等）可通过 `renderOnlySpectrum=false` 时的 GUI 控件实时调整
- 使用 `ImGui::InvisibleButton` 消耗画布区域的点击事件，防止拖拽时移动窗口
- 所有 ImGui 字符串通过 `makeUnique()` 附加 `this` 指针地址，确保多实例不会 ID 冲突
