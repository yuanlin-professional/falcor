# run_clang_format.py 脚本文档

> 路径: `tools/run_clang_format.py`
> 类型: Python 脚本
> 模块: tools

## 功能概述

此脚本是 `clang-format` 代码格式化工具的增强包装器，基于开源项目 [run-clang-format](https://github.com/Sarcasm/run-clang-format) 扩展而来。主要特性：

1. **批量格式化**：支持对多个文件和目录进行递归格式化操作。
2. **并行处理**：使用多进程池（`multiprocessing.Pool`）并行执行格式化，默认使用 CPU 核心数 + 1 个工作进程（Windows 上限制最大 60 个）。
3. **Slang 语言支持**：扩展支持 Falcor 项目使用的 Slang 着色语言文件（`.slang`、`.slangh`），通过 XML 替换机制实现自定义格式化规则。
4. **差异输出**：生成 unified diff 格式的差异输出，支持彩色显示。
5. **就地格式化**：支持 `-i` 参数直接修改源文件。
6. **排除规则**：支持 `.clang-format-ignore` 文件和命令行排除模式，以及白名单机制。

## 主要功能/类

### `class ExitStatus`
| 属性 | 值 | 说明 |
|------|------|------|
| `SUCCESS` | `0` | 无格式差异 |
| `DIFF` | `1` | 存在格式差异 |
| `TROUBLE` | `2` | 执行出错 |

### `class DiffError(Exception)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `message` | `str` | 错误消息 |
| `errs` | `list` | 标准错误输出内容 |

### `class UnexpectedError(Exception)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `message` | `str` | 错误消息 |
| `exc` | `Exception` | 原始异常 |

### `excludes_from_file(ignore_file)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `ignore_file` | `str` | 忽略规则文件路径 |

**返回值**: `[list, list]` - 排除模式列表和白名单模式列表。以 `!` 开头的行为白名单，以 `#` 开头的行为注释。

### `list_files(files, recursive, extensions, exclude, whitelist)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `files` | `list` | 文件或目录列表 |
| `recursive` | `bool` | 是否递归搜索 |
| `extensions` | `list` | 文件扩展名过滤列表 |
| `exclude` | `list` | 排除的 glob 模式列表 |
| `whitelist` | `list` | 白名单路径列表 |

**返回值**: `set` 或 `list` - 匹配的文件路径集合。

### `run_clang_format_diff(args, file)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `args` | `argparse.Namespace` | 命令行参数 |
| `file` | `str` | 要格式化的文件路径 |

**返回值**: `(list, bytes)` - 差异行列表和标准错误输出。核心格式化函数，对单个文件执行 `clang-format` 并生成差异。

### `make_diff(file, original, reformatted)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `file` | `str` | 文件路径 |
| `original` | `list` | 原始内容行列表 |
| `reformatted` | `list` | 格式化后内容行列表 |

**返回值**: `list` - unified diff 格式的差异行。

### `colorize(diff_lines)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `diff_lines` | `iterable` | 差异输出行 |

**返回值**: 生成器，输出带 ANSI 颜色代码的差异行（加粗红色表示删除，绿色表示新增，青色表示位置标记）。

### `print_diff(diff_lines, use_color)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `diff_lines` | `iterable` | 差异输出行 |
| `use_color` | `bool` | 是否使用颜色 |

**返回值**: 无。将差异输出打印到标准输出。

### `main()`
| 参数 | 类型 | 说明 |
|------|------|------|
| 无 | - | - |

**返回值**: `int` - 退出状态码。主入口函数，解析参数、收集文件、并行执行格式化、输出结果。

## 使用示例

```bash
# 递归检查目录中的格式差异
python run_clang_format.py -r Source/Falcor

# 就地格式化所有文件
python run_clang_format.py -r -i Source/Falcor

# 使用指定的 clang-format 可执行文件
python run_clang_format.py --clang-format-executable /usr/bin/clang-format-14 -r Source/

# 排除特定目录
python run_clang_format.py -r --exclude "*/External/*" Source/

# 试运行模式（仅打印命令）
python run_clang_format.py -d Source/Falcor/Core/Device.h

# 使用 8 个并行任务
python run_clang_format.py -r -j 8 Source/
```

## 依赖关系

### 导入的模块
- `argparse` - 命令行参数解析
- `difflib` - 生成 unified diff
- `fnmatch` - glob 模式匹配
- `io` - 文件编码读取
- `errno` - 错误码常量
- `multiprocessing` - 并行处理
- `os` - 文件系统操作
- `signal` - 信号处理
- `subprocess` - 调用 clang-format
- `sys` - 标准 I/O 和退出码
- `traceback` - 异常追踪格式化
- `xml.etree.ElementTree` - 解析 Slang 格式化的 XML 替换输出
- `pathlib.Path` - 路径操作
- `functools.partial` - 偏函数（用于多进程映射）

### 外部依赖
- `clang-format` - LLVM 代码格式化工具（需要在 PATH 中或通过参数指定路径）

## 实现细节

- **Slang 文件处理**：Slang 文件（`.slang`、`.slangh`）被当作 C# 文件传递给 clang-format（`--assume-filename source.cs`），使用 `--output-replacements-xml` 获取 XML 格式的替换建议，然后手动过滤不适用的替换（如 `}` 和 `;` 之间不插入换行）后应用。
- **忽略文件**：读取 `.clang-format-ignore` 文件，支持注释行（`#` 开头）、排除模式和白名单模式（`!` 开头）。
- **并行策略**：使用 `multiprocessing.Pool.imap_unordered` 进行无序并行处理，遇到 `UnexpectedError` 时立即终止进程池。
- **颜色输出**：自动检测终端是否支持颜色（通过 `isatty()`），也可通过 `--color` 参数强制控制。
- **信号处理**：恢复 `SIGINT` 和 `SIGPIPE` 的默认信号处理器，使 Ctrl+C 和管道断开行为正常。
- 基于 MIT 许可证，原作者 Guillaume Papin。
