# RenderGraphIR 源码文档

> 路径: `Source/Falcor/RenderGraph/RenderGraphIR.h` + `RenderGraphIR.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderGraph (渲染图)

## 功能概述

`RenderGraphIR` 是渲染图的中间表示 (Intermediate Representation) 生成器。它将渲染图的构建操作（创建通道、添加边、标记输出等）转换为等价的 Python 脚本代码。生成的 IR 可用于序列化渲染图或在编辑器中记录变更。

## 类与接口

### `RenderGraphIR`

- **继承**: 无
- **职责**: 将渲染图操作转换为 Python 脚本字符串。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `RenderGraphIR(const std::string& name, bool newGraph = true)` | 构造函数。`newGraph=true` 时生成完整的函数定义头 |
| `void createPass(const std::string& passClass, const std::string& passName, const Properties&)` | 生成创建渲染通道的 Python 代码 |
| `void updatePass(const std::string& passName, const Properties&)` | 生成更新渲染通道的 Python 代码 |
| `void removePass(const std::string& passName)` | 生成移除渲染通道的 Python 代码 |
| `void addEdge(const std::string& src, const std::string& dst)` | 生成添加边的 Python 代码 |
| `void removeEdge(const std::string& src, const std::string& dst)` | 生成移除边的 Python 代码 |
| `void markOutput(const std::string& name, TextureChannelFlags mask)` | 生成标记输出的 Python 代码 |
| `void unmarkOutput(const std::string& name)` | 生成取消标记输出的 Python 代码 |
| `std::string getIR()` | 获取最终生成的 Python 脚本字符串 |
| `static std::string getFuncName(const std::string& graphName)` | 获取渲染图对应的 Python 函数名 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mName` | `std::string` | 渲染图名称 |
| `mIR` | `std::string` | 累积的 Python 代码字符串 |
| `mIndentation` | `std::string` | 当前缩进级别 |
| `mGraphPrefix` | `std::string` | 图对象调用前缀（如 `g.`） |

## 依赖关系

### 本文件引用

- `Core/API/Formats.h` - 纹理通道标志
- `Utils/Properties.h` - 属性字典
- `Utils/Scripting/ScriptWriter.h` - 脚本写入工具
- `Utils/StringUtils.h` - 字符串工具

### 被以下文件引用

- `RenderGraphImportExport.cpp` - 导出功能
- `RenderGraphUI.h/cpp` - UI 变更记录

## 实现细节

- **代码生成**: 使用 `ScriptWriter::makeFunc()` 生成 Python 函数调用语句，自动处理参数序列化。
- **函数定义**: `newGraph=true` 时生成 `def render_graph_<name>():` 函数头，并设置 4 空格缩进。函数末尾自动添加 `return g`。
- **函数名生成**: `getFuncName()` 将图名称前缀 `render_graph_`，并将空格、正斜杠、反斜杠替换为下划线。
- **输出标记优化**: `markOutput()` 在掩码为默认值 `RGB` 时省略掩码参数，简化生成的代码。
- **生成示例**:
  ```python
  from pathlib import WindowsPath, PosixPath
  from falcor import *

  def render_graph_MyGraph():
      g = RenderGraph("MyGraph")
      g.create_pass("Pass1", "GBufferRaster", {})
      g.add_edge("Pass1.output", "Pass2.input")
      g.mark_output("Pass2.color")
      return g
  ```
