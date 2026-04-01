# AppData 源码文档

> 路径: `Source/Mogwai/AppData.h`, `Source/Mogwai/AppData.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Mogwai

## 功能概述

`AppData` 类负责管理 Mogwai 应用程序的持久化数据，主要用于存储和维护用户最近打开的脚本文件和场景文件列表。数据以 JSON 格式保存在用户目录下，在应用启动时自动加载，在添加新记录时自动保存。

## 类与接口

### `AppData`

- **继承**: 无
- **职责**: 管理应用程序的持久化配置数据，包括最近使用的脚本路径列表和场景路径列表。数据存储在 JSON 文件中，支持自动加载和保存。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `AppData(const std::filesystem::path& path)` | 构造函数，接收 JSON 数据文件路径，自动创建父目录并从文件加载已有数据 |
| `const std::vector<std::filesystem::path>& getRecentScripts() const` | 获取最近使用的脚本文件路径列表 |
| `const std::vector<std::filesystem::path>& getRecentScenes() const` | 获取最近使用的场景文件路径列表 |
| `void addRecentScript(const std::filesystem::path& path)` | 添加脚本路径到最近使用列表（自动去重、置顶、截断） |
| `void addRecentScene(const std::filesystem::path& path)` | 添加场景路径到最近使用列表（自动去重、置顶、截断） |
| `void addRecentPath(std::vector<std::filesystem::path>& paths, const std::filesystem::path& path)` | 内部通用方法：将路径规范化后添加到列表头部，超过上限则截断，并触发保存 |
| `void removeNonExistingPaths(std::vector<std::filesystem::path>& paths)` | 移除列表中不存在或非规范化形式的路径 |
| `void save()` | 将当前数据保存到文件 |
| `void loadFromFile(const std::filesystem::path& path)` | 从 JSON 文件加载数据，解析失败时输出警告日志 |
| `void saveToFile(const std::filesystem::path& path)` | 将数据序列化为 JSON 并写入文件（缩进 4 空格） |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mPath` | `std::filesystem::path` | 持久化 JSON 文件的存储路径 |
| `mRecentScripts` | `std::vector<std::filesystem::path>` | 最近使用的脚本文件列表 |
| `mRecentScenes` | `std::vector<std::filesystem::path>` | 最近使用的场景文件列表 |

## 依赖关系

### 本文件引用

- `Falcor.h` — Falcor 框架核心头文件
- `<filesystem>` — 标准文件系统库
- `<nlohmann/json.hpp>` — JSON 序列化/反序列化库
- `<fstream>` — 文件 I/O

### 被以下文件引用

- `Source/Mogwai/Mogwai.h` — `Renderer` 类包含 `AppData` 成员
- `Source/Mogwai/Mogwai.cpp` — 在渲染器中使用 `AppData` 管理最近文件
- `Source/Mogwai/MogwaiSettings.cpp` — 设置 UI 中通过 `AppData` 显示最近文件菜单

## 实现细节

- **最大记录数**: 最近文件列表上限为 25 条（`kMaxRecentFiles`）。
- **JSON 结构**: 文件包含两个键 `"recentScripts"` 和 `"recentScenes"`，各自存储路径字符串数组。
- **路径规范化**: 添加路径时使用 `std::filesystem::canonical()` 确保路径唯一性；加载时通过 `removeNonExistingPaths()` 清理无效和非规范化路径。
- **自动保存**: 每次添加新路径后立即触发 `save()`，确保数据不丢失。
- **错误处理**: JSON 解析失败时仅输出警告（`logWarning`），不会中断程序。
