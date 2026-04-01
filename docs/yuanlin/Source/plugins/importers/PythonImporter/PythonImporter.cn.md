# PythonImporter 源码文档

> 路径: `Source/plugins/importers/PythonImporter/PythonImporter.h`, `PythonImporter.cpp`
> 类型: C++ 头文件 + 实现
> 模块: plugins/importers/PythonImporter

## 功能概述

Python 场景脚本（`.pyscene`）的导入器插件。通过嵌入式 Python 解释器执行场景脚本，脚本中可使用 `sceneBuilder` 对象和 `falcor` 模块来程序化构建场景。支持从文件和内存缓冲区导入，并防止递归导入。

## 类与接口

### `PythonImporter`

- **继承**: `Importer`
- **插件注册**: `FALCOR_PLUGIN_CLASS`，插件名 `"PythonImporter"`
- **支持格式**: pyscene

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static std::unique_ptr<Importer> create()` | 工厂方法 |
| `void importScene(const path&, SceneBuilder&, const map&)` | 从文件导入：读取脚本内容并执行 |
| `void importSceneFromMemory(const void*, size_t, string_view, SceneBuilder&, const map&)` | 从内存缓冲区导入 |

#### 私有方法

| 方法签名 | 说明 |
|----------|------|
| `void importInternal(const string& script, const path&, SceneBuilder&)` | 内部导入实现：检查旧格式、管理导入作用域、执行 Python 脚本 |

### `ScopedImport`（内部类）

- **职责**: RAII 管理导入作用域，防止递归导入，管理搜索路径栈和全局 `SceneBuilder` 引用。

## 依赖关系

### 本文件引用

- `Scene/Importer.h` - 导入器基类
- `GlobalState.h` - 全局 Python SceneBuilder
- `Utils/Scripting/Scripting.h` - Python 脚本执行引擎

## 实现细节

- 脚本执行前自动运行 `from falcor import *` 导入 Falcor Python 绑定。
- 脚本中通过 `sceneBuilder` 变量访问 `SceneBuilder` 实例。
- 支持检测旧版格式头部（`# filename.extension`），遇到时报错引导用户迁移。
- 递归导入检测使用全局路径集合 `sImportPaths`。
- 搜索路径通过 `pushAssetResolver`/`popAssetResolver` 管理，确保相对路径解析正确。
