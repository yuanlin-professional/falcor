# pymacro.py 脚本文档

> 路径: `tools/pymacro.py`
> 类型: Python 脚本
> 模块: tools

## 功能概述

此脚本是一个 Python 宏处理工具，用于在 C++ 源代码中展开内嵌的 Python 宏代码。开发者可以在 C++ 文件中嵌入 Python 代码块，脚本会执行这些代码并将 `print()` 的输出替换到源文件中，实现代码生成功能。

宏的格式为：
```cpp
/* <<<PYMACRO
<Python 代码>
>>> */
<生成的 C++ 代码（会被替换）>
/* <<<PYMACROEND>>> */
```

脚本使用状态机解析文件，识别宏标记，执行 Python 代码，并用输出替换标记之间的内容。

## 主要功能/类

### `class Capturing`

上下文管理器，用于捕获 Python 代码执行期间的 `stdout` 输出。

| 方法 | 说明 |
|------|------|
| `__enter__()` | 重定向 `sys.stdout` 到 `StringIO` |
| `__exit__(*args)` | 恢复原始 `stdout`，将捕获的输出按行存储 |

继承自 `list`，退出后可直接迭代获取捕获的输出行。

### `class State(Enum)`

状态机的三种状态：

| 值 | 说明 |
|------|------|
| `IDLE` | 空闲状态，正常复制源文件行 |
| `HEADER` | 宏头部状态，正在收集 Python 代码 |
| `CONTENT` | 内容状态，跳过旧的生成内容直到遇到结束标记 |

### `process_file(path, dry_run)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `path` | `Path` | 要处理的文件路径 |
| `dry_run` | `bool` | 为 `True` 时仅打印结果不写入文件 |

**返回值**: 无。解析文件中的宏标记，执行 Python 代码，用输出替换宏区域的内容。

### `run(args)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `args` | `argparse.Namespace` | 命令行参数，包含 `files` 和 `dry_run` |

**返回值**: `int` - 固定返回 0。

### `main()`
| 参数 | 类型 | 说明 |
|------|------|------|
| 无 | - | - |

**返回值**: `int` - 退出码。解析命令行参数并调用 `run()`。

## 使用示例

```bash
# 处理单个文件
python pymacro.py source.cpp

# 处理多个文件
python pymacro.py file1.h file2.cpp file3.h

# 试运行模式（仅打印不写入）
python pymacro.py --dry-run source.cpp
```

C++ 文件中的宏示例：
```cpp
/* <<<PYMACRO
print("const int table[] = {")
print("    " + ", ".join(str(i) for i in range(8)))
print("};")
>>> */
const int table[] = {
    0, 1, 2, 3, 4, 5, 6, 7
};
/* <<<PYMACROEND>>> */
```

## 依赖关系

### 导入的模块
- `sys` - 标准输出重定向和退出码
- `re` - 正则表达式匹配宏标记
- `argparse` - 命令行参数解析
- `pathlib.Path` - 文件路径操作
- `enum.Enum` - 状态机状态定义
- `io.StringIO` - 输出捕获

## 实现细节

- **正则表达式标记**：
  - 宏开始：`^\s*\/\*\s*<<<PYMACRO\s*$`
  - 宏代码结束：`^\s*>>>\s*\*\/\s*$`
  - 宏内容结束：`^\s*\/\*\s+<<<PYMACROEND>>>\s*\*\/\s*$`
- **状态机流程**：IDLE -> 遇到 `<<<PYMACRO` -> HEADER（收集 Python 代码）-> 遇到 `>>>` -> CONTENT（用 Python 输出替换，跳过旧内容）-> 遇到 `<<<PYMACROEND>>>` -> IDLE。
- **代码执行**：使用 `compile()` 和 `eval()` 执行收集的 Python 代码，通过 `Capturing` 类捕获 `print()` 输出。
- **差异检测**：只有当处理后的内容与原始内容不同时才写入文件。
