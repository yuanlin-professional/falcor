# PathResolving 源码文档

> 路径: `Source/Falcor/Utils/PathResolving.h` + `Source/Falcor/Utils/PathResolving.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils

## 功能概述

提供文件路径解析工具，支持环境变量展开、搜索路径解析和文件路径查找。遵循 Renderman 语义：支持 `@`（标准搜索路径）和 `&`（当前搜索路径）特殊字符，以及 `${ENV_VARIABLE}` 格式的环境变量。

## 类与接口

### `ResolvedPaths` 结构体

| 成员 | 类型 | 说明 |
|------|------|------|
| `resolved` | `std::vector<path>` | 已解析的有效路径 |
| `invalid` | `std::vector<string>` | 无法解析的路径（仅用于错误报告） |

### 类型别名

| 名称 | 类型 | 说明 |
|------|------|------|
| `EnvVarResolver` | `std::optional<string>(const string&)` | 环境变量解析函数类型 |
| `FileChecker` | `bool(const path&)` | 文件存在性检查函数类型 |

### 自由函数

| 函数签名 | 说明 |
|----------|------|
| `bool resolveEnvVariables(string& str, EnvVarResolver, string_view beginToken, string_view endToken)` | 就地解析字符串中的环境变量，返回标记是否匹配 |
| `ResolvedPaths resolveSearchPaths(const vector<path>& current, const vector<string>& update, const vector<path>& standard, EnvVarResolver)` | 解析搜索路径更新，支持 `&`、`@` 和分号分隔 |
| `path resolvePath(const vector<path>& searchPaths, const path& cwd, const string& filePath, FileChecker)` | 将文件路径解析为绝对路径 |
| `bool checkFileExists(const path& path)` | 默认文件存在性检查 |

## 依赖关系

### 本文件引用

- `Core/Macros.h`、`Core/Error.h`、`Core/Platform/OS.h` — 平台抽象
- `Utils/StringUtils.h` — 字符串分割

### 被以下文件引用

- 资源加载系统、着色器查找等需要路径解析的模块

## 实现细节

- 绝对路径（以 `/` 或盘符 `X:` 开头）直接使用
- 相对路径（以 `.` 开头）仅相对当前工作目录解析
- 其他路径按搜索路径顺序查找，返回第一个存在的文件
- 环境变量解析器可注入，便于测试
- 使用 `std::filesystem::weakly_canonical` 规范化路径
