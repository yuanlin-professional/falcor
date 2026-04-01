# ninja_log_analyzer.py 脚本文档

> 路径: `tools/ninja_log_analyzer.py`
> 类型: Python 脚本
> 模块: tools

## 功能概述

此脚本用于分析 Ninja 构建系统生成的日志文件（`.ninja_log`），帮助开发者了解构建性能。主要功能：

1. 解析 Ninja v5 格式的日志文件，提取每个构建目标的开始时间、结束时间和持续时间。
2. 计算并显示总构建时间。
3. 按耗时从长到短列出指定数量的最慢构建目标，帮助定位构建瓶颈。

脚本只分析最后一次构建的日志条目（检测到新一轮构建时会重置），并跳过相同哈希值的重复条目。

## 主要功能/类

### `Entry`（命名元组）
| 字段 | 类型 | 说明 |
|------|------|------|
| `name` | `str` | 构建目标名称 |
| `hash` | `str` | 条目哈希值 |
| `start` | `float` | 开始时间（秒） |
| `end` | `float` | 结束时间（秒） |
| `duration` | `float` | 持续时间（秒） |

### `read_entries(path)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `path` | `str` | Ninja 日志文件路径 |

**返回值**: `list[Entry]` 或 `None` - 解析出的日志条目列表，文件打开失败或格式错误时返回 `None`。

### `format_duration(duration)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `duration` | `float` | 持续时间（秒） |

**返回值**: `str` - 格式化为 `MM:SS.ss` 的时间字符串。

### `run(args)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `args` | `argparse.Namespace` | 命令行参数，包含 `logfile` 和 `count` |

**返回值**: 无。主执行函数，读取日志、排序并打印结果。

## 使用示例

```bash
# 分析默认显示前 10 个最慢目标
python ninja_log_analyzer.py build/.ninja_log

# 显示前 20 个最慢目标
python ninja_log_analyzer.py build/.ninja_log -c 20
```

输出示例：
```
Ninja log analyzer:
05:32.10 Total build time
01:23.45 Source/Falcor/Core/API/Device.cpp.obj
00:58.12 Source/Falcor/Scene/Scene.cpp.obj
...
```

## 依赖关系

### 导入的模块
- `logging` - 日志记录（错误和警告信息）
- `argparse` - 命令行参数解析
- `collections.namedtuple` - 定义 `Entry` 数据结构
- `math.floor` - 时间格式化中的向下取整

## 实现细节

- **日志格式**：Ninja v5 日志每行包含 5 个制表符分隔的字段：开始时间（毫秒）、结束时间（毫秒）、未使用字段、目标名称、哈希值。
- **新一轮构建检测**：当当前条目的结束时间小于上一条目的结束时间时，判定为新一轮构建开始，清空已有条目。
- **去重逻辑**：通过比较相邻条目的哈希值跳过重复条目。
- **时间转换**：日志中的时间为毫秒级整数，脚本将其转换为秒（除以 1000）。
- 脚本在模块级别直接执行（无 `__main__` 保护）。
