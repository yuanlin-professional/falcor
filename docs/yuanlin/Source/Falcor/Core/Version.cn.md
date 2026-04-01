# Version 源码文档

> 路径: `Source/Falcor/Core/Version.h` + `Source/Falcor/Core/Version.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core

## 功能概述

提供 Falcor 框架的版本信息查询接口。支持短版本字符串（主版本.次版本）和包含 Git 提交信息的长版本字符串。

## 宏定义

| 宏 | 值 | 说明 |
|------|------|------|
| `FALCOR_MAJOR_VERSION` | 8 | 主版本号 |
| `FALCOR_MINOR_VERSION` | 0 | 次版本号 |

## 全局函数

| 函数签名 | 说明 |
|----------|------|
| `getVersionString()` | 返回短版本字符串，格式为 `"8.0"` |
| `getLongVersionString()` | 返回包含 Git 信息的长版本字符串，格式为 `"8.0 (commit: xxx, branch: yyy)"` |

## 依赖关系

### 本文件引用
- `Core/Macros.h` — `FALCOR_API` 导出宏
- `git_version.h` — 构建时生成的 Git 版本信息（`GIT_VERSION_AVAILABLE`、`GIT_VERSION_COMMIT`、`GIT_VERSION_BRANCH`、`GIT_VERSION_DIRTY`）
- `fmt/format.h` — 字符串格式化

### 被以下文件引用
- `Core/SampleApp.cpp` — 启动时输出版本日志

## 实现细节

- 版本字符串使用 `static` 局部变量延迟初始化，保证整个生命周期只构建一次。
- 若 Git 命令行不可用（`GIT_VERSION_AVAILABLE` 为 false），长版本字符串会注明 "git version unknown"。
- 长版本字符串会标注是否包含未提交的修改（`GIT_VERSION_DIRTY`）。
