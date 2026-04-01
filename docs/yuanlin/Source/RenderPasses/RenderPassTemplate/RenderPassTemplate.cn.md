# RenderPassTemplate 源码文档

> 路径: `Source/RenderPasses/RenderPassTemplate/RenderPassTemplate.h` + `RenderPassTemplate.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/RenderPassTemplate

## 功能概述

RenderPassTemplate 是创建新渲染通道的模板/骨架代码。它提供了一个最小化的 `RenderPass` 子类实现，包含所有必需的虚函数空实现，作为开发新渲染通道的起点。

## 类与接口

### `RenderPassTemplate`

- **继承**: `RenderPass`
- **职责**: 作为新渲染通道的开发模板

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `RenderPassTemplate(ref<Device>, const Properties&)` | 构造函数（空实现） |
| `Properties getProperties() const` | 返回空属性字典 |
| `RenderPassReflection reflect(const CompileData&)` | 返回空反射信息（注释中提示添加输入/输出） |
| `void compile(RenderContext*, const CompileData&)` | 空实现 |
| `void execute(RenderContext*, const RenderData&)` | 空实现（注释中提示获取资源） |
| `void renderUI(Gui::Widgets&)` | 空实现 |
| `void setScene(RenderContext*, const ref<Scene>&)` | 空实现 |
| `bool onMouseEvent(const MouseEvent&)` | 返回 false |
| `bool onKeyEvent(const KeyboardEvent&)` | 返回 false |

#### 关键成员

无私有成员。

## 依赖关系

### 本文件引用

- `Falcor.h`
- `RenderGraph/RenderPass.h`

### 被以下文件引用

- 作为独立插件通过 `registerPlugin` 注册

## 实现细节

此文件是纯模板代码，所有方法均为空实现或返回默认值。开发者应以此为基础，添加自己的输入/输出声明、着色器和处理逻辑。
