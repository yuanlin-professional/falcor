# Importer 源码文档

> 路径: `Source/Falcor/Scene/Importer.h` + `Source/Falcor/Scene/Importer.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene

## 功能概述

Importer 是场景资产导入器的基类。它采用插件化架构，不同的导入器通过注册文件扩展名来支持不同的资产格式。当需要导入某种格式的资产时，系统根据扩展名自动查找并创建对应的导入器实例。

## 类与接口

### `Importer`

- **继承**: 无（使用 `FALCOR_PLUGIN_BASE_CLASS` 宏实现插件基类）
- **职责**: 定义场景导入接口，管理导入器插件的注册与创建

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `virtual void importScene(const path& path, SceneBuilder& builder, const map<string, string>& materialToShortName) = 0` | 从文件路径导入场景（纯虚函数） |
| `virtual void importSceneFromMemory(const void* buffer, size_t byteSize, string_view extension, SceneBuilder& builder, const map<string, string>& materialToShortName)` | 从内存缓冲区导入场景（默认抛出未实现异常） |
| `static unique_ptr<Importer> create(string_view extension, const PluginManager& pm)` | 工厂方法，根据文件扩展名创建对应的导入器 |
| `static vector<string> getSupportedExtensions(const PluginManager& pm)` | 获取所有已注册导入器支持的文件扩展名列表 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `PluginInfo::desc` | `string` | 导入器描述 |
| `PluginInfo::extensions` | `vector<string>` | 支持的文件扩展名列表 |

## 依赖关系

### 本文件引用

- `SceneBuilder.h` - 场景构建器
- `ImporterError.h` - 导入异常类
- `Core/Plugin.h` - 插件管理基础设施
- `Utils/Scripting/ScriptBindings.h` - Python 脚本绑定

### 被以下文件引用

- 各具体导入器插件（如 AssimpImporter、USDImporter 等）
- `SceneBuilder` - 调用导入器加载场景

## 实现细节

- `create()` 遍历 PluginManager 中注册的所有 Importer 类型，查找匹配扩展名的导入器并实例化
- 通过 `FALCOR_SCRIPT_BINDING` 宏将 `ImporterError` 注册为 Python 异常类型
