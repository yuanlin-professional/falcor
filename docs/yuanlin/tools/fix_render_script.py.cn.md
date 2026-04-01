# fix_render_script.py 脚本文档

> 路径: `tools/fix_render_script.py`
> 类型: Python 脚本
> 模块: tools

## 功能概述

此脚本用于自动修复 Falcor 渲染脚本（render script）中的过时语法。主要完成两项转换工作：

1. **枚举类型转换**：将 Python 枚举类型（如 `CompositeMode.Value`）替换为字符串形式（如 `'Value'`）。支持近 50 种枚举类型的自动转换，同时处理特殊映射（如 `CullMode.CullNone` 转换为 `'None'`）。
2. **可序列化结构体转换**：将 Python 构造函数调用形式（如 `StructName(key=value)`）转换为字典形式（如 `{'key': value}`）。

脚本通过 glob 模式匹配文件，自动检测是否为渲染脚本（包含 `from falcor import *`），并提供试运行和强制模式。

## 主要功能/类

### `is_render_script(text)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `text` | `str` | 文件内容文本 |

**返回值**: `bool` - 文本中是否包含 `from falcor import *`，用于判断是否为 Falcor 渲染脚本。

### `update_enums(text)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `text` | `str` | 待处理的文件内容 |

**返回值**: `str` - 替换枚举引用后的文本。遍历 `ENUMS` 列表和 `ENUM_MAP` 字典，使用正则表达式将 `EnumName.Value` 格式替换为 `'Value'` 字符串格式。

### `update_serializable_structs(text)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `text` | `str` | 待处理的文件内容 |

**返回值**: `str` - 替换结构体构造调用后的文本。将 `StructName(key=value, ...)` 转换为 `{'key': value, ...}` 字典形式。

### `run(args)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `args` | `argparse.Namespace` | 命令行参数，包含 `path`、`force`、`dry_run` |

**返回值**: 无。主执行函数，遍历匹配文件并执行修复。

## 使用示例

```bash
# 修复指定路径下的所有渲染脚本
python fix_render_script.py "scripts/**/*.py"

# 试运行模式（不写入文件）
python fix_render_script.py "scripts/**/*.py" --dry-run

# 强制模式（即使文件未被检测为渲染脚本也进行处理）
python fix_render_script.py "scripts/**/*.py" --force
```

## 依赖关系

### 导入的模块
- `re` - 正则表达式处理
- `glob` - 文件路径模式匹配
- `argparse` - 命令行参数解析

## 实现细节

- **ENUMS 列表**：包含约 48 种枚举类型名称，涵盖渲染管线中的各类配置枚举（色调映射、降噪方法、采样模式等）。
- **ENUM_MAP 字典**：处理枚举值名称与字符串值不一致的特殊情况，例如 `CullMode.CullNone` 需要映射为 `'None'` 而非 `'CullNone'`。
- **SERIALIZABLE_STRUCTS 列表**：包含 8 种可序列化结构体类型，用于将构造调用转换为字典。
- **正则表达式**：枚举替换使用 `([^'"]){EnumName}\.(\w+)` 模式，确保不会替换已经是字符串形式的引用。
- 脚本在模块级别直接执行（无 `__main__` 保护），解析参数后调用 `run()` 函数。
