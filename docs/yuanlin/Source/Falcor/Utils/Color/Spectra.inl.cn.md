# Spectra.inl 源码文档

> 路径: `Source/Falcor/Utils/Color/Spectra.inl`
> 类型: C++ 内联数据文件 (.inl)
> 模块: Utils/Color

## 功能概述

本文件是一个纯数据文件,包含大量预计算的光谱数据数组,供 `Spectrum.cpp` 通过 `#include` 内联引入。文件中定义了 CIE 颜色匹配函数、CIE 标准光源、玻璃折射率光谱、金属复折射率光谱 (eta 和 k) 以及多种数码相机的光谱响应曲线数据。

## 数据内容

### CIE 颜色匹配函数

| 数组名 | 采样数 | 说明 |
|--------|--------|------|
| `CIE_X[471]` | 471 | CIE 1931 X 颜色匹配函数 (360-830nm) |
| `CIE_Y[471]` | 471 | CIE 1931 Y 颜色匹配函数 (360-830nm) |
| `CIE_Z[471]` | 471 | CIE 1931 Z 颜色匹配函数 (360-830nm) |

### CIE 标准光源

| 数组名 | 说明 |
|--------|------|
| `CIE_Illum_A` | CIE 标准光源 A (钨丝灯) |
| `CIE_Illum_D5000` | CIE D50 标准光源 |
| `CIE_Illum_D6500` | CIE D65 标准光源 |
| `CIE_Illum_F1` ~ `CIE_Illum_F12` | CIE F 系列荧光灯光源 (F1-F12) |
| `ACES_Illum_D60` | ACES D60 光源 |

### 玻璃折射率光谱

| 数组名 | 说明 |
|--------|------|
| `GlassBK7_eta` | BK7 玻璃折射率 |
| `GlassBAF10_eta` | BAF10 玻璃折射率 |
| `GlassFK51A_eta` | FK51A 玻璃折射率 |
| `GlassLASF9_eta` | LASF9 玻璃折射率 |
| `GlassSF5_eta` | SF5 玻璃折射率 |
| `GlassSF10_eta` | SF10 玻璃折射率 |
| `GlassSF11_eta` | SF11 玻璃折射率 |

### 金属复折射率光谱

| 材质 | eta 数组 | k 数组 | 说明 |
|------|----------|--------|------|
| Ag (银) | `Ag_eta` | `Ag_k` | 银的折射率和消光系数 |
| Al (铝) | `Al_eta` | `Al_k` | 铝的折射率和消光系数 |
| Au (金) | `Au_eta` | `Au_k` | 金的折射率和消光系数 |
| Cu (铜) | `Cu_eta` | `Cu_k` | 铜的折射率和消光系数 |
| CuZn (黄铜) | `CuZn_eta` | `CuZn_k` | 黄铜的折射率和消光系数 |
| MgO (氧化镁) | `MgO_eta` | `MgO_k` | 氧化镁的折射率和消光系数 |
| TiO2 (二氧化钛) | `TiO2_eta` | `TiO2_k` | 二氧化钛的折射率和消光系数 |

### 数码相机光谱响应曲线

涵盖多款相机的 RGB 三通道光谱响应数据:
- Canon EOS 100D, 1DX Mark II, 200D, 200D Mark II, 5D, 5D Mark II/III/IV, 5DS, EOS M
- Hasselblad L1D-20c
- Nikon D810, D850
- Sony ILCE-6400, ILCE-7M3, ILCE-7RM3, ILCE-9

## 依赖关系

### 被以下文件引用
- `Source/Falcor/Utils/Color/Spectrum.cpp` - 通过 `#include "Spectra.inl"` 在匿名命名空间中引入

## 实现细节

- 所有光谱数据以交错格式存储 (波长, 值, 波长, 值, ...),由 `PiecewiseLinearSpectrum::fromInterleaved` 解析。
- CIE 颜色匹配函数为均匀采样 (1nm 间隔,360-830nm,共 471 个采样点),由 `DenseleySampledSpectrum` 使用。
- 在 MSVC 编译器下禁用 C4305 警告 (浮点数截断),因为数据使用 `float` 字面量。
