# SceneBuilderDump 源码文档

> 路径: `Source/Falcor/Scene/SceneBuilderDump.h` + `Source/Falcor/Scene/SceneBuilderDump.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene

## 功能概述

SceneBuilderDump 是一个调试工具类，用于将 SceneBuilder 的内容序列化为文本形式，方便开发者检查场景构建过程中的数据。输出格式不稳定，随调试需求变化。

## 类与接口

### `SceneBuilderDump`

- **继承**: 无
- **职责**: 将 SceneBuilder 的几何数据转储为调试文本

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static map<string, string> getDebugContent(const SceneBuilder&)` | 返回几何体名称到其文本序列化的映射 |

## 依赖关系

### 本文件引用

- `SceneBuilder` - 场景构建器（前向声明）

### 被以下文件引用

- 调试/测试代码
