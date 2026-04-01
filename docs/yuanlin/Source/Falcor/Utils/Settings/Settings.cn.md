# Settings 源码文档

> 路径: `Source/Falcor/Utils/Settings/Settings.h` + `Source/Falcor/Utils/Settings/Settings.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Settings

## 功能概述

Falcor 全局设置管理系统。提供选项（Options）管理、属性过滤（Attribute Filters）管理和搜索路径（Search Directories）管理三大功能。支持从 JSON 文件、Python 字典/列表等多种来源加载配置。内置设置栈机制，支持通过 `ScopedSettings` RAII 类实现设置的临时覆盖与恢复。

## 类与接口

### `Settings`

- **继承**: 无
- **导出宏**: `FALCOR_API`
- **职责**: 全局配置中心，管理选项、属性过滤器和搜索路径

#### 类型别名

| 别名 | 实际类型 | 说明 |
|------|----------|------|
| `Options` | `settings::Attributes` | 全局选项类型 |
| `Attributes` | `settings::Attributes` | 属性类型 |
| `TypeError` | `settings::detail::TypeError` | 类型错误异常 |
| `SearchDirectories` | `std::vector<std::filesystem::path>` | 搜索路径列表 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static Settings& getGlobalSettings()` | 获取全局单例设置实例，首次调用时从运行时目录和用户主目录加载 `settings.json` |
| `const Options& getOptions() const` | 获取当前活跃的选项集 |
| `std::optional<T> getOption(std::string_view optionName) const` | 获取指定选项值 |
| `T getOption(std::string_view optionName, const T& def) const` | 带默认值的选项获取 |
| `void addOptions(const nlohmann::json& options)` | 从 JSON 添加选项，自动展平嵌套字典 |
| `void addOptions(const pybind11::dict& options)` | 从 Python 字典添加选项 |
| `void addOptions(const pybind11::list& options)` | 从 Python 列表添加选项 |
| `bool addOptions(const std::filesystem::path& path)` | 从 JSON 文件加载选项，成功返回 `true` |
| `void clearOptions()` | 清空当前选项 |
| `std::optional<T> getAttribute(std::string_view shapeName, std::string_view attributeName) const` | 获取指定形状的属性值 |
| `Attributes getAttributes(std::string_view shapeName) const` | 获取指定形状的全部属性 |
| `void addFilteredAttributes(...)` | 添加过滤属性（支持 JSON、Python 字典/列表、文件路径） |
| `void clearFilteredAttributes()` | 清空所有过滤属性 |
| `const SearchDirectories& getSearchDirectories(std::string_view category)` | 获取指定类别的搜索路径 |
| `void pushSettingsStack()` | 将当前设置压入栈（复制当前状态） |
| `void popSettingsStack()` | 弹出设置栈顶，恢复上一层设置 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mData` | `std::vector<SettingsData>` | 设置栈，存储选项和属性过滤器的层级状态 |
| `mStandardSearchDirectories` | `std::map<std::string, SearchDirectories, std::less<>>` | 标准搜索路径映射 |
| `mSearchDirectories` | `std::map<std::string, SearchDirectories, std::less<>>` | 用户搜索路径映射 |

#### 内部结构

| 结构体 | 说明 |
|--------|------|
| `SettingsData` | 包含 `Options mOptions` 和 `AttributeFilter mAttributeFilters`，代表一层设置状态 |

---

### `ScopedSettings`

- **职责**: RAII 封装，构造时压入设置栈，析构时弹出，实现设置的临时覆盖

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `ScopedSettings(Settings& settings)` | 构造时调用 `pushSettingsStack()` |
| `~ScopedSettings()` | 析构时调用 `popSettingsStack()` |

## 依赖关系

### 本文件引用

- `AttributeFilters.h` — `AttributeFilter` 类
- `Core/Macros.h` — Falcor 宏
- `Core/Error.h` — 断言和错误检查
- `nlohmann/json.hpp` — JSON 解析
- `Utils/StringUtils.h` — 字符串连接工具
- `Utils/PathResolving.h` — 路径解析工具
- `Utils/Scripting/ScriptBindings.h` — Python 脚本绑定
- `pybind11/pybind11.h` — Python 绑定
- `pybind11_json/pybind11_json.hpp` — Python-JSON 互转

### 被以下文件引用

- 应用程序和渲染器通过 `Settings::getGlobalSettings()` 访问全局配置

## 实现细节

- **全局初始化**: `getGlobalSettings()` 使用静态局部变量实现懒加载单例。首次调用时依次从 `getRuntimeDirectory()/settings.json` 和 `~/.falcor/settings.json` 加载配置。
- **搜索路径更新**: `updateSearchPaths` 解析 JSON 中的 `searchpath` 和 `standardsearchpath` 键（支持嵌套字典和冒号分隔格式），通过 `resolveSearchPaths` 验证和解析路径。
- **选项覆盖**: 添加新选项时先通过 `removePrefix` 移除同前缀的旧选项，再合并新值，确保嵌套字典被正确覆盖。
- **Python 绑定**: 通过 `FALCOR_SCRIPT_BINDING(Settings)` 注册 `addOptions`、`addFilteredAttributes`、`clearOptions`、`clearFilteredAttributes` 等方法到 Python。
- **JSON 注释支持**: 从文件加载时启用 `ignore comments` 参数，支持 JSON 文件中的注释。
