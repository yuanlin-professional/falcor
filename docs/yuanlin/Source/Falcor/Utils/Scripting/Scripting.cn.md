# Scripting 源码文档

> 路径: `Source/Falcor/Utils/Scripting/Scripting.h` + `Source/Falcor/Utils/Scripting/Scripting.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Scripting

## 功能概述

提供 Falcor 的核心脚本引擎，封装了嵌入式 Python 解释器的生命周期管理、脚本执行上下文管理、以及脚本的运行与解释功能。该模块是 Falcor 脚本系统的入口点，支持执行 Python 脚本字符串、脚本文件，以及交互式脚本解释。

## 类与接口

### `Scripting`

- **继承**: 无
- **职责**: 管理 Python 解释器的启动/关闭，提供默认和当前脚本执行上下文，执行脚本代码。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static void start()` | 启动脚本引擎。初始化 Python 解释器，设置 Python 搜索路径，导入 falcor 模块到默认上下文 |
| `static void shutdown()` | 关闭脚本引擎。释放默认上下文并终止 Python 解释器 |
| `static bool isRunning()` | 返回脚本引擎是否正在运行 |
| `static Context& getDefaultContext()` | 获取默认脚本执行上下文 |
| `static Context getCurrentContext()` | 获取当前正在执行的脚本的上下文（包装 `pybind11::globals()`） |
| `static RunResult runScript(std::string_view script, Context& context, bool captureOutput)` | 执行脚本字符串。可选捕获 stdout/stderr 输出 |
| `static RunResult runScriptFromFile(const std::filesystem::path& path, Context& context, bool captureOutput)` | 从文件执行脚本。自动设置 `__file__` 变量 |
| `static std::string interpretScript(const std::string& script, Context& context)` | 以交互式解释器模式执行脚本并返回结果字符串（用于控制台） |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `kFileExtensionFilters` | `FileDialogFilterVec` | 脚本文件对话框过滤器（`.py`） |
| `sRunning` | `static bool` | 引擎运行状态标志 |
| `sDefaultContext` | `static std::unique_ptr<Context>` | 默认脚本上下文 |

### `Scripting::Context`

- **继承**: 无
- **职责**: 封装一个 Python 全局字典（`globals`），作为脚本执行的命名空间。支持向脚本上下文中传入/取出变量。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `Context(pybind11::dict globals)` | 从现有 Python 字典构造上下文 |
| `Context()` | 创建空上下文，自动拷贝 `__builtins__` |
| `template<typename T> std::vector<ObjectDesc<T>> getObjects()` | 获取上下文中所有可转换为类型 `T` 的对象 |
| `template<typename T> void setObject(const std::string& name, T obj)` | 在上下文中设置命名对象 |
| `template<typename T> T getObject(const std::string& name) const` | 从上下文中获取命名对象 |
| `bool containsObject(const std::string& name) const` | 检查上下文中是否包含指定名称的对象 |

### `Scripting::RunResult`

- **职责**: 存储脚本执行的捕获输出。

| 成员 | 类型 | 说明 |
|------|------|------|
| `out` | `std::string` | 标准输出内容 |
| `err` | `std::string` | 标准错误内容 |

### `RedirectStream`（实现文件内部类）

- **职责**: RAII 风格的流重定向工具，将 Python 的 stdout/stderr 重定向到 `io.StringIO` 缓冲区以捕获输出。

## 依赖关系

### 本文件引用

- `Core/Macros.h` — 导出宏
- `Core/Error.h` — 错误处理
- `Core/Platform/OS.h` — 运行时目录获取（`getRuntimeDirectory`）、文件读取（`readFile`）
- `Utils/StringUtils.h` — 字符串工具
- `Utils/StringFormatters.h` — 字符串格式化
- `<pybind11/pybind11.h>` / `<pybind11/embed.h>` — pybind11 嵌入式解释器

### 被以下文件引用

- `Utils/Scripting/Console.cpp` — 控制台使用 `interpretScript` 执行命令
- 渲染管线和应用程序框架中的脚本加载逻辑

## 实现细节

- Python 解释器通过 `pybind11::initialize_interpreter()` / `pybind11::finalize_interpreter()` 管理生命周期。
- Python Home 路径根据编译配置决定：若定义了 `FALCOR_PYTHON_EXECUTABLE` 则使用其父目录，否则使用运行时目录下的 `pythondist`。
- 启动时自动将运行时目录下的 `python` 子目录加入 Python 搜索路径。
- 设置环境变量 `FALCOR_EMBEDDED_PYTHON=1` 以告知 falcor Python 模块当前运行在嵌入式解释器中。
- `interpretScript` 使用 Python 标准库 `code.InteractiveInterpreter` 实现交互式解释，捕获 stdout 和 stderr 的输出。
- `Context` 析构时会手动将 globals 字典中所有键设为 `None`，以避免持有对象引用导致内存泄漏。
- `runScriptFromFile` 执行完成后会清除 `__file__` 变量。
