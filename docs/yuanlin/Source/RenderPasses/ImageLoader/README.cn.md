# ImageLoader - 图像加载通道

## 功能概述

ImageLoader 是一个将磁盘图像文件加载到渲染图纹理中的渲染通道。它作为渲染图的数据源节点，为下游通道提供静态图像输入。主要功能包括：

- **图像文件加载**：支持通过配置路径或运行时文件对话框加载图像
- **sRGB 控制**：可选择以 sRGB 或线性色彩空间加载图像
- **Mipmap 生成**：可选自动生成多级纹理
- **数组切片/Mip 级别选择**：支持纹理数组和 Mipmap 级别的逐层浏览
- **输出尺寸控制**：支持默认尺寸（由下游决定）或固定为图像原始分辨率，分辨率不匹配时自动双线性缩放
- **图像预览**：在 UI 面板中显示 320x320 缩略图及格式/尺寸信息

## 文件清单

| 文件名 | 类型 | 说明 |
|--------|------|------|
| `ImageLoader.h` | C++ 头文件 | 定义 `ImageLoader` 类，包含纹理引用、路径、加载选项等成员 |
| `ImageLoader.cpp` | C++ 源文件 | 实现图像加载、blit 输出、UI 渲染、属性序列化/反序列化 |
| `CMakeLists.txt` | 构建配置 | CMake 构建脚本 |

## 依赖关系

### 框架依赖
- `Falcor.h` - Falcor 核心框架
- `RenderGraph/RenderPass.h` - 渲染通道基类
- `RenderGraph/RenderPassHelpers.h` - I/O 尺寸计算辅助工具（`IOSize` 枚举、`calculateIOSize()`）
- `Core/AssetResolver.h` - 资源路径解析器

### 输入/输出通道
| 通道名 | 方向 | 说明 |
|--------|------|------|
| `dst` | 输出 | 目标纹理（格式由用户配置或自动检测） |

该通道无输入通道，是一个纯数据源节点。

## 关键类与接口

### `ImageLoader` 类
继承自 `RenderPass`，核心接口：

| 方法 | 说明 |
|------|------|
| `reflect()` | 声明单个输出通道 `dst`，尺寸根据 `mOutputSizeSelection` 和图像原始尺寸决定 |
| `compile()` | 检查是否已加载图像，未加载则抛出异常 |
| `execute()` | 通过 `blit()` 将加载的纹理（指定 mip/array slice）拷贝到输出纹理 |
| `renderUI()` | 提供输出尺寸下拉框、sRGB/Mipmap 复选框、文件加载按钮、图像预览和信息显示 |
| `getProperties()` | 序列化所有配置项用于渲染图保存 |
| `loadImage()` | 通过 `AssetResolver` 解析路径并调用 `Texture::createFromFile()` 加载图像 |

### 可序列化属性
| 属性名 | 类型 | 说明 |
|--------|------|------|
| `outputSize` | IOSize 枚举 | 输出尺寸模式（Default / Fixed） |
| `outputFormat` | ResourceFormat | 输出纹理格式 |
| `filename` | 文件路径 | 图像文件路径 |
| `mips` | bool | 是否生成 Mipmap |
| `srgb` | bool | 是否以 sRGB 方式加载 |
| `arrayIndex` | uint32 | 纹理数组切片索引 |
| `mipLevel` | uint32 | Mipmap 级别 |
