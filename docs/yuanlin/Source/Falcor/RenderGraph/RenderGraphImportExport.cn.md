# RenderGraphImportExport 源码文档

> 路径: `Source/Falcor/RenderGraph/RenderGraphImportExport.h` + `RenderGraphImportExport.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderGraph (渲染图)

## 功能概述

提供渲染图的导入（从 Python 脚本加载）和导出（序列化为 Python 脚本）功能。`RenderGraphImporter` 从脚本文件中加载渲染图，`RenderGraphExporter` 将渲染图序列化为 Python 中间表示 (IR) 并保存到文件。

## 类与接口

### `RenderGraphImporter`

- **继承**: 无
- **职责**: 从 Python 脚本文件导入渲染图。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<RenderGraph> import(std::string graphName, std::filesystem::path path, std::string funcName)` | 从脚本文件导入指定名称的渲染图。若路径为空则搜索 `<graphName>.py`，若函数名为空则调用 `render_graph_<graphName>()` |
| `static std::vector<ref<RenderGraph>> importAllGraphs(const std::filesystem::path&)` | 从脚本文件导入所有渲染图 |

### `RenderGraphExporter`

- **继承**: 无
- **职责**: 将渲染图导出为 Python 脚本。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static std::string getIR(const ref<RenderGraph>&)` | 获取渲染图的 Python 中间表示字符串 |
| `static std::string getFuncName(const std::string& graphName)` | 获取渲染图的 Python 函数名 |
| `static bool save(const ref<RenderGraph>&, std::filesystem::path)` | 将渲染图保存为 Python 脚本文件 |

## 依赖关系

### 本文件引用

- `RenderGraph.h` - 渲染图主类
- `RenderGraphIR.h` - 中间表示生成
- `Core/AssetResolver.h` - 资源路径解析
- `Utils/Scripting/Scripting.h` - Python 脚本执行

### 被以下文件引用

- `RenderGraph.cpp` - 渲染图主类

## 实现细节

- **导入流程**: 将脚本路径通过 `AssetResolver` 解析为绝对路径，读取文件内容并附加调用函数的代码，然后通过 `Scripting::runScript()` 执行，从脚本上下文中提取 `RenderGraph` 对象。
- **重试机制**: 导入失败时弹出消息框询问用户是否重试，使用 `while(true)` 循环实现。
- **导出格式**: 导出时遍历图的节点和边数据，使用 `RenderGraphIR` 生成 Python 脚本。输出的脚本包含函数定义和自动调用代码，兼容 Mogwai 的 `m.addGraph()` 接口。
- **命名约定**: Python 函数名格式为 `render_graph_<graphName>`，路径中的空格和斜杠替换为下划线。
