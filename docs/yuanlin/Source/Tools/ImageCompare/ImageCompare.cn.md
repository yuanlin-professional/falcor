# ImageCompare 源码文档

> 路径: `Source/Tools/ImageCompare/ImageCompare.cpp`
> 类型: C++ 实现
> 模块: Tools/ImageCompare

## 功能概述

这是一个独立的命令行图像比较工具。它加载两张图像并使用指定的误差度量（MSE、RMSE、MAE、MAPE）计算差异。支持浮点和 LDR 格式图像，可选择是否包含 Alpha 通道，并能生成误差热力图。主要类 `Image` 封装了 FreeImage 库的图像加载/保存功能，将所有图像转换为 RGBA32F 内部格式进行处理。

## 类与接口

### `Image`

- **职责**: 封装图像数据的加载、保存和像素访问

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `loadFromFile(const path&)` | 使用 FreeImage 加载图像并转换为 RGBA32F |
| `saveToFile(const path&, bool)` | 将图像保存为指定格式文件 |
| `create(uint32_t, uint32_t)` | 创建指定尺寸的空白图像 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mWidth` | `uint32_t` | 图像宽度 |
| `mHeight` | `uint32_t` | 图像高度 |
| `mData` | `unique_ptr<float[]>` | RGBA32F 像素数据 |


## 结构体

- `MSE`: 均方误差（Mean Squared Error）计算仿函数
- `RMSE`: 相对均方误差（Relative Mean Squared Error）计算仿函数
- `MAE`: 平均绝对误差（Mean Absolute Error）计算仿函数
- `MAPE`: 平均绝对百分比误差（Mean Absolute Percentage Error）计算仿函数
- `ErrorMetric`: 误差度量描述结构体，包含名称、描述和比较函数


## 依赖关系

### 本文件引用

- `FreeImage.h`
- `args.hxx`
- `iostream`
- `memory`
- `string`
- `vector`
- `stdexcept`
- `map`
- `functional`
- `filesystem`
- `cmath`
- `cstring`


## 实现细节

工具通过 `args::ArgumentParser` 解析命令行参数。图像比较流程：

1. 使用 FreeImage 库加载两张输入图像，统一转换为 RGBA32F 格式
2. 逐像素计算指定误差度量值
3. 将总误差输出到标准输出
4. 可选生成误差热力图（蓝-青-绿-黄-红色阶）

支持的误差度量：MSE（均方误差）、RMSE（相对均方误差）、MAE（平均绝对误差）、MAPE（平均绝对百分比误差）。
