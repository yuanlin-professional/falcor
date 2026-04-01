# ResolvePass 源码文档

> 路径: `Source/Falcor/RenderPasses/ResolvePass.h` + `Source/Falcor/RenderPasses/ResolvePass.cpp`
> 类型: C++ 头文件 + 实现
> 模块: 渲染通道 (RenderPasses)

## 功能概述

`ResolvePass` 是一个将多重采样（MSAA）纹理解析为单采样纹理的渲染通道。它接收一个多重采样的输入纹理，调用 GPU 的 `resolveResource` 操作将其转换为普通单采样纹理输出。此通道常用于 MSAA 渲染流水线的最终解析阶段。

## 类与接口

### `ResolvePass`

- **继承**: `RenderPass`
- **职责**: 执行多重采样纹理到单采样纹理的解析操作

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `ResolvePass(ref<Device> pDevice)` | 构造函数 |
| `static ref<ResolvePass> create(ref<Device>, const Properties&)` | 工厂方法 |
| `void setFormat(ResourceFormat format)` | 设置输入/输出纹理格式 |
| `RenderPassReflection reflect(const CompileData&)` | 声明渲染通道的输入输出：`src`（多重采样纹理）和 `dst`（单采样纹理） |
| `void execute(RenderContext*, const RenderData&)` | 执行解析操作，调用 `resolveResource` |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mFormat` | `ResourceFormat` | 纹理格式，默认为 `Unknown` |

#### 输入/输出端口

| 端口名 | 方向 | 说明 |
|--------|------|------|
| `src` | 输入 | 多重采样源纹理 |
| `dst` | 输出 | 单采样目标纹理 |

## 依赖关系

### 本文件引用

- `Core/Macros.h` — 宏定义
- `Core/API/Formats.h` — 资源格式定义
- `RenderGraph/RenderPass.h` — 渲染通道基类
- `Core/API/RenderContext.h` — 渲染上下文
- `Utils/Logger.h` — 日志工具

### 被以下文件引用

- Falcor 渲染图系统中需要 MSAA 解析的场景

## 实现细节

- 若输入纹理的采样数为 1（非多重采样），打印警告日志并跳过处理
- 若输入或输出纹理缺失，打印警告日志
- 该通道通过 `FALCOR_PLUGIN_CLASS` 宏注册为插件，但不通过动态库加载
