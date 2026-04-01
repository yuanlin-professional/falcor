# PythonImporter - Python 脚本场景导入器

## 功能概述

PythonImporter 是 Falcor 的 Python 脚本场景导入插件，允许用户通过编写 Python 脚本以编程方式构建场景。脚本在执行时可以访问 `sceneBuilder` 对象（即 `Falcor::SceneBuilder` 实例），并通过 `from falcor import *` 导入 Falcor 的完整 Python 绑定 API，从而灵活地创建网格、材质、灯光、相机等场景元素。

### 支持的文件格式

`pyscene`

### 主要功能

- **脚本执行**: 通过 Falcor 的 `Scripting` 模块执行 Python 脚本，脚本上下文中注入 `sceneBuilder` 对象供场景构建使用。
- **文件导入** (`importScene`): 从 `.pyscene` 文件路径加载并执行 Python 脚本。
- **内存导入** (`importSceneFromMemory`): 从内存缓冲区直接执行 Python 脚本字符串。
- **递归导入保护**: 通过 `ScopedImport` RAII 机制跟踪当前导入路径，检测并阻止循环导入。
- **嵌套导入支持**: 维护导入深度计数器，正确管理嵌套场景脚本的资源搜索路径和全局 SceneBuilder 状态。
- **旧格式检测**: 解析脚本首行的 legacy 头注释（`# filename.extension` 格式），检测到旧语法时输出明确的升级提示。
- **搜索路径管理**: 自动将脚本所在目录添加为资源搜索路径，确保脚本中引用的相对路径能正确解析。

## 文件清单

| 文件名 | 类型 | 说明 |
|--------|------|------|
| `PythonImporter.h` | 头文件 | 声明 `PythonImporter` 类，注册插件元信息，声明 `importScene`、`importSceneFromMemory` 和私有的 `importInternal` 方法 |
| `PythonImporter.cpp` | 源文件 | 实现脚本加载、递归导入检测（`ScopedImport`）、legacy 头解析以及通过 `Scripting` 模块执行 Python 脚本的完整流程 |
| `CMakeLists.txt` | 构建文件 | CMake 构建配置 |

## 依赖关系

### Falcor 内部依赖

- `Scene/Importer.h` - 导入器基类接口
- `GlobalState.h` - 全局状态管理（`setActivePythonSceneBuilder`）
- `Utils/Scripting/Scripting.h` - Python 脚本执行引擎，提供 `runScript` 和 `runScriptFromFile` 接口
