# Utils 源码文档

> 路径: `Source/RenderPasses/Utils/Utils.cpp`
> 类型: C++ 实现（插件注册入口）
> 模块: RenderPasses/Utils

## 功能概述

Utils.cpp 是工具渲染通道模块的插件注册入口文件。它将三个工具渲染通道注册到 Falcor 的插件系统中。

## 注册的渲染通道

| 渲染通道 | 说明 |
|----------|------|
| `CrossFade` | 交叉淡入淡出通道，在两个输入之间进行时间相关的混合 |
| `Composite` | 合成通道，将两个缓冲区按可配置的运算方式混合 |
| `GaussianBlur` | 高斯模糊通道，可分离的两遍高斯模糊 |

## 依赖关系

### 本文件引用

- `CrossFade/CrossFade.h`
- `Composite/Composite.h`
- `GaussianBlur/GaussianBlur.h`

## 实现细节

GaussianBlur 额外注册了 Python 脚本绑定（`GaussianBlur::registerBindings`），支持通过脚本控制核宽度和 sigma 参数。
