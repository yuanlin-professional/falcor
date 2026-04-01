# BlitPass 源码文档

> 路径: `Source/RenderPasses/BlitPass/BlitPass.h` + `Source/RenderPasses/BlitPass/BlitPass.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/BlitPass

## 功能概述

BlitPass 是一个简单的渲染通道，用于将输入纹理拷贝（blit）到输出纹理。该通道主要用于纹理格式转换，支持在不同资源格式之间进行纹理复制操作。用户可以通过脚本属性配置纹理过滤模式和输出格式。

## 类与接口

### `BlitPass`

- **继承**: `RenderPass`
- **职责**: 将源纹理 blit 到目标纹理，支持格式转换和过滤模式选择

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `BlitPass(ref<Device> pDevice, const Properties& props)` | 构造函数，解析属性参数 |
| `Properties getProperties() const override` | 序列化当前配置（过滤模式、输出格式） |
| `RenderPassReflection reflect(const CompileData&) override` | 声明输入 `src` 和输出 `dst` 纹理通道 |
| `void execute(RenderContext*, const RenderData&) override` | 执行 blit 操作，将 `src` 纹理通过指定过滤模式复制到 `dst` |
| `void renderUI(Gui::Widgets&) override` | 渲染 UI 下拉框以选择过滤模式 |
| `TextureFilteringMode getFilter() const` | 获取当前纹理过滤模式 |
| `void setFilter(TextureFilteringMode filter)` | 设置纹理过滤模式 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mFilter` | `TextureFilteringMode` | 纹理过滤模式，默认为 `Linear`（线性过滤） |
| `mOutputFormat` | `ResourceFormat` | 输出纹理格式，默认为 `Unknown`（自动匹配） |

## 依赖关系

### 本文件引用

- `Falcor.h` — Falcor 核心头文件
- `RenderGraph/RenderPass.h` — 渲染通道基类

### 被以下文件引用

- 渲染图脚本中通过插件注册机制使用

## 实现细节

- **插件注册**: 通过 `registerPlugin` 导出函数注册为 `RenderPass` 插件，同时注册 Python 绑定以支持脚本控制过滤模式。
- **Python 绑定**: 暴露 `filter` 属性，支持通过字符串设置过滤模式（如 `"Linear"`、`"Point"`）。
- **输入/输出**: 输入通道名为 `src`，输出通道名为 `dst`。输出格式可通过 `outputFormat` 属性指定。
- **执行逻辑**: 在 `execute` 中调用 `RenderContext::blit()`，使用全屏矩形区域进行拷贝。如果缺少输入或输出资源，会输出警告日志。
- **属性序列化**: 支持 `filter` 和 `outputFormat` 两个属性的序列化与反序列化。
