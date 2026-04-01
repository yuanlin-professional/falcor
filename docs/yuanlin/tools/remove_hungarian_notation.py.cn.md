# remove_hungarian_notation.py 脚本文档

> 路径: `tools/remove_hungarian_notation.py`
> 类型: Python 脚本
> 模块: tools

## 功能概述

此脚本用于从 C/C++ 源代码中自动移除匈牙利命名法前缀。匈牙利命名法是一种在变量名前添加类型前缀的命名规范（如 `pDevice` 中的 `p` 表示指针）。脚本会递归处理指定目录下的所有 `.cpp` 和 `.h` 文件。

处理规则：
- 以 `p` 开头后跟大写字母的变量（如 `pDevice`）：移除 `p` 并将首字母变为小写（`device`）。
- 以 `m`、`s`、`g` 开头后跟 `p` 再跟大写字母的变量（如 `mpDevice`）：保留首字母前缀，移除 `p`（`mDevice`）。

## 主要功能/类

### `remove_hungarian_notation(filename)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `filename` | `str` | 要处理的文件路径 |

**返回值**: 无。读取文件内容，使用正则表达式替换匈牙利命名法标识符，然后写回文件。

### `process_directory(path)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `path` | `str` | 要递归处理的目录路径 |

**返回值**: 无。递归遍历目录，对所有 `.cpp` 和 `.h` 文件调用 `remove_hungarian_notation()`。

## 使用示例

```bash
# 处理整个源代码目录
python remove_hungarian_notation.py Source/Falcor

# 处理特定子目录
python remove_hungarian_notation.py Source/Falcor/Core
```

转换示例：
| 原始标识符 | 转换后 |
|-----------|--------|
| `pDevice` | `device` |
| `pTexture` | `texture` |
| `mpDevice` | `mDevice` |
| `spInstance` | `sInstance` |
| `gpContext` | `gContext` |

## 依赖关系

### 导入的模块
- `os` - 目录遍历（`os.walk`）
- `re` - 正则表达式匹配和替换
- `argparse` - 命令行参数解析

## 实现细节

- **正则表达式模式**：`([^a-zA-Z0-9_"'])([msg]?[p][A-Z][a-zA-Z0-9_]*)` - 匹配非标识符字符后面跟随的可选 `m`/`s`/`g` 前缀 + `p` + 大写字母开头的标识符。
- **替换逻辑**：
  - 仅以 `p` 开头：移除 `p`，将下一个字符转为小写（`pFoo` -> `foo`）。
  - 以 `m`/`s`/`g` + `p` 开头：保留第一个字符，跳过 `p`，保留其余部分（`mpFoo` -> `mFoo`）。
  - 其他情况：不做替换。
- **排除范围**：模式中的 `[^a-zA-Z0-9_"']` 前缀确保不会替换字符串字面量中的内容或更长标识符的一部分。
- **文件过滤**：仅处理 `.cpp` 和 `.h` 扩展名的文件。
