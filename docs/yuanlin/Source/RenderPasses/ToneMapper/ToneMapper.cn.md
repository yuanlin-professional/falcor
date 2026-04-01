# ToneMapper 源码文档

> 路径: `Source/RenderPasses/ToneMapper/ToneMapper.h` + `ToneMapper.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/ToneMapper

## 功能概述

本文件实现了色调映射（Tone Mapping）渲染通道。该通道将 HDR 颜色缓冲区映射到 [0, 1] 范围，支持自动曝光、眼适应、白平衡和多种色调映射算子。渲染流程包含两个全屏通道：亮度计算通道（可选，用于自动曝光）和色调映射主通道。

## 类与接口

### `ToneMapper`

- **继承**: `RenderPass`
- **职责**: 对渲染图中的颜色缓冲区执行色调映射

#### 枚举类型

| 枚举 | 说明 |
|------|------|
| `Operator` | 色调映射算子（别名为 `ToneMapperOperator`）：Linear、Reinhard、ReinhardModified、HejiHableAlu、HableUc2、Aces |
| `ExposureMode::AperturePriority` | 光圈优先模式（修改 EV 时保持光圈不变） |
| `ExposureMode::ShutterPriority` | 快门优先模式（修改 EV 时保持快门不变） |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `ToneMapper(ref<Device>, const Properties&)` | 构造函数，解析属性、创建亮度和色调映射通道 |
| `Properties getProperties() const` | 序列化所有通道属性 |
| `RenderPassReflection reflect(const CompileData&)` | 声明输入（src）和输出（dst，支持自定义尺寸和格式） |
| `void execute(RenderContext*, const RenderData&)` | 执行色调映射：可选的亮度计算 -> 颜色变换 -> 色调映射 |
| `void renderUI(Gui::Widgets&)` | 渲染 UI：曝光组、颜色分级组、色调映射组 |
| `void setScene(RenderContext*, const ref<Scene>&)` | 从场景元数据中读取 ISO、光圈、快门参数 |
| `void setExposureCompensation(float)` | 设置曝光补偿（F-stops） |
| `void setAutoExposure(bool)` | 启用/禁用自动曝光 |
| `void setExposureValue(float)` | 设置曝光值（根据曝光模式自动调整光圈或快门） |
| `void setOperator(Operator)` | 设置色调映射算子 |
| `void setWhiteBalance(bool)` | 启用/禁用白平衡 |
| `void setWhitePoint(float)` | 设置白点色温（K） |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mExposureCompensation` | `float` | 曝光补偿（F-stops），范围 [-12, 12] |
| `mAutoExposure` | `bool` | 自动曝光开关 |
| `mExposureValue` | `float` | 曝光值（EV），由光圈和快门导出 |
| `mFilmSpeed` | `float` | 胶片感光度（ISO），范围 [1, 6400] |
| `mFNumber` | `float` | 光圈值，范围 [0.1, 100] |
| `mShutter` | `float` | 快门速度倒数，范围 [0.1, 10000] |
| `mWhiteBalance` | `bool` | 白平衡开关 |
| `mWhitePoint` | `float` | 白点色温（K），范围 [1905, 25000] |
| `mOperator` | `Operator` | 当前色调映射算子（默认 Aces） |
| `mClamp` | `bool` | 是否将输出钳制到 [0, 1] |
| `mWhiteMaxLuminance` | `float` | ReinhardModified 算子参数 |
| `mWhiteScale` | `float` | HableUc2 算子参数 |
| `mColorTransform` | `float3x3` | 最终颜色变换矩阵（包含白平衡和曝光） |
| `mUseSceneMetadata` | `bool` | 是否使用场景元数据初始化参数 |
| `mpToneMapPass` | `ref<FullScreenPass>` | 色调映射全屏通道 |
| `mpLuminancePass` | `ref<FullScreenPass>` | 亮度计算全屏通道 |

## 依赖关系

### 本文件引用

- `Falcor.h` — 核心框架
- `ToneMapperParams.slang` — 色调映射参数定义（主机/设备共享）
- `Core/Enum.h` — 枚举工具
- `RenderGraph/RenderPass.h` — 渲染通道基类
- `RenderGraph/RenderPassHelpers.h` — IO 尺寸辅助工具
- `Core/Pass/FullScreenPass.h` — 全屏通道
- `Utils/Color/ColorUtils.h` — 颜色工具（白平衡计算）
- 着色器: `Luminance.ps.slang`、`ToneMapping.ps.slang`

### 被以下文件引用

- 渲染图配置脚本通过 `ToneMapper` 名称引用

## 实现细节

1. **曝光计算**: 手动曝光公式为 `scale = (filmSpeed / 100) / (shutter * fNumber^2)`。曝光值 `EV = log2(shutter * fNumber^2)`。在光圈优先模式下修改 EV 会调整快门，在快门优先模式下则调整光圈。

2. **自动曝光**: 先通过亮度通道计算每个像素的对数亮度值，写入带 mipmap 的 FBO。色调映射通道读取最高 mip 级别获取场景平均亮度，使用曝光常数 `kExposureKey = 0.042` 进行自适应曝光。

3. **白平衡**: 使用 `calculateWhiteBalanceTransformRGB_Rec709` 计算 Rec.709 色彩空间下的白平衡变换矩阵。

4. **颜色变换**: 最终颜色变换矩阵 = 白平衡矩阵 * 曝光补偿缩放 * 手动曝光缩放。

5. **通道重建**: 更改色调映射算子、自动曝光开关或钳制开关时需要重建着色器（通过宏定义控制）。其他参数变化仅需更新常量缓冲区。

6. **Python 脚本接口**: 通过 pybind11 暴露大量属性：exposureCompensation、autoExposure、filmSpeed、whiteBalance、operator、clamp 等。

7. **场景元数据**: 加载场景时，如果 `mUseSceneMetadata` 为 true，会从场景元数据中读取 ISO、光圈和快门参数。
