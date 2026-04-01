# install_package.py 脚本文档

> 路径: `tools/packman/bootstrap/install_package.py`
> 类型: Python 脚本
> 模块: tools/packman/bootstrap

## 功能概述

此脚本是 NVIDIA Packman 包管理器的引导安装组件，负责将下载的压缩包安全地解压并安装到指定目录。主要特性：

1. **安全安装**：使用临时暂存目录（staging directory），解压完成后通过原子重命名操作将包移动到最终位置，避免安装过程中断导致的不完整安装。
2. **SHA-256 校验**：对安装包进行哈希校验，确保包的完整性和来源可信。
3. **重试机制**：重命名操作支持最多 100 次重试（每次间隔 0.1 秒），处理并发安装时的竞态条件。
4. **安全路径检查**：拒绝安装到系统 PATH 中包含的可执行文件路径，防止误覆盖系统工具。

## 主要功能/类

### `class StagingDirectory`

上下文管理器，管理临时暂存目录的生命周期。

| 方法 | 说明 |
|------|------|
| `__init__(staging_path)` | 创建暂存根目录 |
| `__enter__()` | 在暂存根目录下创建临时文件夹 |
| `get_temp_folder_path()` | 获取临时文件夹路径 |
| `promote_and_rename(folder_name)` | 将临时文件夹重命名为最终名称 |
| `__exit__(...)` | 退出时清理残留的临时文件夹 |

### `remove_directory_item(path)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `path` | `str` | 要删除的文件或目录路径 |

**返回值**: 无。递归删除文件或目录，处理权限问题和文件夹链接。

### `rename_folder(staging_dir, folder_name)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `staging_dir` | `StagingDirectory` | 暂存目录实例 |
| `folder_name` | `str` | 目标文件夹名称 |

**返回值**: 无。尝试将暂存目录重命名为最终名称，如果目标已存在则忽略（假设其他进程已完成安装）。

### `call_with_retry(op_name, func, retry_count, retry_delay)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `op_name` | `str` | 操作名称（用于日志） |
| `func` | `Callable` | 要执行的函数 |
| `retry_count` | `int` | 最大重试次数（默认 3） |
| `retry_delay` | `float` | 重试间隔秒数（默认 20） |

**返回值**: `Any` - 函数执行结果。带重试的通用操作执行器。

### `rename_folder_with_retry(staging_dir, folder_name)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `staging_dir` | `StagingDirectory` | 暂存目录实例 |
| `folder_name` | `str` | 目标文件夹名称 |

**返回值**: 无。带重试机制的文件夹重命名（100 次重试，0.1 秒间隔）。

### `generate_sha256_for_file(file_path)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `file_path` | `str` 或 `os.PathLike` | 文件路径 |

**返回值**: `str` - 文件的 SHA-256 十六进制摘要。

### `install_common_module(package_path, install_path)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `package_path` | `str` | 压缩包文件路径 |
| `install_path` | `str` | 安装目标路径 |

**返回值**: 无。校验 SHA-256 后将压缩包解压到目标路径。

## 使用示例

```bash
# 作为命令行工具使用（由 packman 系统自动调用）
python install_package.py <package_path> <install_path>
```

```python
# 作为模块使用
from install_package import install_common_module
install_common_module("/path/to/package.zip", "/path/to/install")
```

## 依赖关系

### 导入的模块
- `logging` - 日志记录
- `zipfile` - ZIP 文件解压
- `tempfile` - 临时目录创建
- `sys` - 命令行参数
- `os` - 文件系统操作
- `stat` - 文件权限常量
- `time` - 重试延迟
- `hashlib` - SHA-256 校验
- `typing` - 类型注解（`Any`、`Callable`、`Union`）

## 实现细节

- **硬编码 SHA-256**：`install_common_module` 中包含一个硬编码的 SHA-256 校验值 `COMMON_SHA256`，只有匹配此值的包才会被安装，确保安全性。
- **并发安全**：通过 `StagingDirectory` 模式和重试机制，支持多个 Packman 进程同时安装同一个包。
- **权限处理**：删除文件/目录前会尝试 `chmod` 设置完整权限，处理只读文件的删除。
- **路径安全**：主入口检查安装路径是否在系统 PATH 中，防止覆盖可执行文件目录。
- 版权归属 NVIDIA CORPORATION，使用 Apache License 2.0 许可。
