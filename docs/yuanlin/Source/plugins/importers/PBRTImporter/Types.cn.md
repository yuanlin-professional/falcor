# Types 源码文档

> 路径: `Source/plugins/importers/PBRTImporter/Types.h`
> 类型: C++ 头文件（仅头文件）
> 模块: plugins/importers/PBRTImporter

## 功能概述

定义 pbrt 导入器使用的基础类型，包括浮点类型别名、文件位置结构体、颜色空间占位符、光谱类型和路径解析器类型。

## 类与接口

### `FileLoc`

- **职责**: 记录 pbrt 文件中的精确位置（文件名:行:列）。

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `filename` | `string_view` | 文件名 |
| `line` | `uint32_t` | 行号（默认 1） |
| `column` | `uint32_t` | 列号（默认 0） |

### 类型别名

| 别名 | 实际类型 | 说明 |
|------|---------|------|
| `Float` | `float` | pbrt 浮点类型 |
| `Resolver` | `function<path(const path&)>` | 路径解析回调 |
| `Spectrum` | `variant<float3, PiecewiseLinearSpectrum, BlackbodySpectrum>` | 光谱数据 |

### 枚举

| 枚举 | 说明 |
|------|------|
| `SpectrumType` | 光谱类型：Illuminant（光源）、Albedo（反照率）、Unbounded（无界） |

### `RGBColorSpace`

- 前向声明的占位结构体，当前始终使用 RGB Rec.709。

## 依赖关系

### 被以下文件引用

- `Parameters.h`、`Parser.h`、`Builder.h`、`Helpers.h` - 基础类型
