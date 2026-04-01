# RenderPassTemplate -- 渲染通道模板

## 功能概述

RenderPassTemplate 是一个最小化的渲染通道骨架模板，为开发者创建新的 Falcor 渲染通道提供起始代码。该模板展示了渲染通道插件的标准结构，包括插件注册、属性序列化、资源反射、执行逻辑和 UI 渲染等所有必要接口的空实现。

### 设计目的

- 作为新渲染通道开发的起点
- 展示 Falcor 渲染通道插件的标准结构和接口
- 所有虚函数均提供空实现或最小实现，方便开发者按需填充

## 文件清单

| 文件 | 类型 | 说明 |
|------|------|------|
| `RenderPassTemplate.h` | C++ 头文件 | 模板类声明，展示所有可重写的虚函数 |
| `RenderPassTemplate.cpp` | C++ 源文件 | 模板类最小实现与插件注册 |
| `CMakeLists.txt` | 构建配置 | CMake 插件构建定义 |

## 依赖关系

| 依赖项 | 说明 |
|--------|------|
| `Falcor.h` | Falcor 核心框架 |
| `RenderGraph/RenderPass.h` | 渲染通道基类 |

## 关键类与接口

### `RenderPassTemplate` (继承自 `RenderPass`)

```
FALCOR_PLUGIN_CLASS(RenderPassTemplate, "RenderPassTemplate", "Insert pass description here.");
```

**所有可重写的虚函数接口：**

| 方法 | 模板实现 | 说明 |
|------|----------|------|
| `create(ref<Device>, const Properties&)` | 创建实例 | 静态工厂方法 |
| `getProperties() const` | 返回空 Properties | 属性序列化（用于保存/加载渲染图） |
| `reflect(const CompileData&)` | 空反射器（注释示例） | 声明输入/输出资源需求 |
| `compile(RenderContext*, const CompileData&)` | 空实现 `{}` | 编译阶段回调（资源尺寸确定后） |
| `execute(RenderContext*, const RenderData&)` | 空实现（注释示例） | 每帧执行渲染逻辑 |
| `renderUI(Gui::Widgets&)` | 空实现 `{}` | 渲染 GUI 控件 |
| `setScene(RenderContext*, const ref<Scene>&)` | 空实现 `{}` | 场景加载/切换回调 |
| `onMouseEvent(const MouseEvent&)` | 返回 false | 鼠标事件处理 |
| `onKeyEvent(const KeyboardEvent&)` | 返回 false | 键盘事件处理 |

### 使用方法

1. 复制 `RenderPassTemplate` 目录并重命名
2. 修改类名和 `FALCOR_PLUGIN_CLASS` 宏中的插件名与描述
3. 在 `reflect()` 中定义输入/输出资源
4. 在 `execute()` 中实现渲染逻辑
5. 在上层 `CMakeLists.txt` 中添加 `add_subdirectory(YourPass)`
