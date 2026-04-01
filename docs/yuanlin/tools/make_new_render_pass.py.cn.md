# make_new_render_pass.py 脚本文档

> 路径: `tools/make_new_render_pass.py`
> 类型: Python 脚本
> 模块: tools

## 功能概述

此脚本用于基于模板快速创建新的 Falcor 渲染通道（Render Pass）项目。工作流程如下：

1. 从 `Source/RenderPasses/RenderPassTemplate` 模板目录复制所有文件到新目录。
2. 将模板文件中所有 `RenderPassTemplate` 字符串替换为用户指定的新名称。
3. 文件名中的 `RenderPassTemplate` 也会被替换为新名称。
4. 自动将新子目录添加到 `Source/RenderPasses/CMakeLists.txt` 中，并保持字母排序。

## 主要功能/类

### `create_project(name)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `name` | `str` | 新渲染通道的名称 |

**返回值**: `bool` - 创建成功返回 `True`，目标目录已存在则返回 `False`。

### `main()`
| 参数 | 类型 | 说明 |
|------|------|------|
| 无 | - | - |

**返回值**: `int` - 退出码，成功为 0，失败为 1。解析命令行参数并调用 `create_project()`。

## 使用示例

```bash
# 创建名为 MyRenderPass 的新渲染通道
python make_new_render_pass.py MyRenderPass
```

执行后将生成目录 `Source/RenderPasses/MyRenderPass/`，其中包含从模板复制并重命名的所有文件。

## 依赖关系

### 导入的模块
- `sys` - 获取脚本路径
- `argparse` - 命令行参数解析
- `pathlib.Path` - 路径操作

## 实现细节

- **模板目录**：固定使用 `Source/RenderPasses/RenderPassTemplate` 作为模板源。
- **路径计算**：通过 `sys.argv[0]` 的父目录向上一级定位到项目根目录，然后拼接 `Source/RenderPasses` 路径。
- **EXCLUDE_EXT**：排除扩展名列表（当前为空），可用于过滤不需要复制的文件类型。
- **CMakeLists.txt 更新**：读取现有内容，追加 `add_subdirectory(name)` 行，按不区分大小写的字母顺序排序后写回。空行会被过滤掉。
