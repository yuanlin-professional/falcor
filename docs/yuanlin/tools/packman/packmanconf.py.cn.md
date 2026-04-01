# packmanconf.py 脚本文档

> 路径: `tools/packman/packmanconf.py`
> 类型: Python 脚本
> 模块: tools/packman

## 功能概述

此脚本是 NVIDIA Packman 包管理器的配置引导模块，用于将 Packman 初始化到 Python 环境中。主要工作流程：

1. 检查 Python 版本兼容性（要求 3.10.0 至 3.11.2）。
2. 确定包的本地缓存根目录（支持 Windows、macOS、Linux 三个平台）。
3. 从配置文件中读取 Packman 版本号。
4. 如果 packman-common 模块尚未安装，则从 NVIDIA 服务器下载并解压安装。
5. 将模块路径添加到 `sys.path`，使后续可以直接 `import packmanapi`。

## 主要功能/类

### `init()`
| 参数 | 类型 | 说明 |
|------|------|------|
| 无 | - | - |

**返回值**: 无。初始化 Packman 配置，调用成功后即可使用 `packmanapi` 模块。检查 Python 版本兼容性，设置环境变量，必要时下载安装 packman-common 模块。

### `get_packages_root(conf_dir)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `conf_dir` | `str` | 配置文件所在目录 |

**返回值**: `str` - 包缓存根目录路径。按平台确定路径：
- Windows: `<驱动器>:\packman-repo`
- macOS: `~/Library/Application Support/packman-cache`
- Linux: `$XDG_HOME_CACHE/packman` 或 `~/.cache/packman`
- 可通过环境变量 `PM_PACKAGES_ROOT` 覆盖。

### `get_module_dir(conf_dir, packages_root, version)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `conf_dir` | `str` | 配置文件所在目录 |
| `packages_root` | `str` | 包缓存根目录 |
| `version` | `str` | Packman 版本号 |

**返回值**: `str` - packman-common 模块目录路径。如果模块尚未安装，会从 `bootstrap.packman.nvidia.com` 下载并通过 `install_package.py` 安装。

### `get_version(conf_dir)`
| 参数 | 类型 | 说明 |
|------|------|------|
| `conf_dir` | `str` | 配置文件所在目录 |

**返回值**: `str` - Packman 版本号。从 `packman` 或 `packman.sh` 启动脚本中解析 `PM_PACKMAN_VERSION` 变量。

## 使用示例

```python
import sys
sys.path.insert(0, "tools/packman")

import packmanconf
packmanconf.init()

# 初始化完成后即可使用 packman API
import packmanapi
packmanapi.set_verbosity_level(packmanapi.VERBOSITY_HIGH)
```

## 依赖关系

### 导入的模块
- `os` - 文件系统操作和环境变量
- `platform` - 操作系统平台检测
- `sys` - Python 版本检查和模块路径操作

### 运行时依赖
- `tools/packman/bootstrap/install_package.py` - 包安装功能（按需动态加载）
- `urllib.request` - HTTP 下载（按需动态导入）
- `tempfile` - 临时文件管理（按需动态导入）

## 实现细节

- **版本要求**：严格限制 Python 版本为 3.10.x 或 3.11.0-3.11.2，其他版本会抛出 `RuntimeError`。
- **环境变量**：设置 `PM_INSTALL_PATH` 指向配置目录；读取 `PM_PACKAGES_ROOT` 获取自定义缓存路径。
- **延迟下载**：只有在本地缓存中找不到对应版本的 packman-common 模块时才会触发下载。
- **动态加载**：使用 `importlib.machinery.SourceFileLoader` 动态加载 `install_package.py` 模块。
- **下载源**：从 `http://bootstrap.packman.nvidia.com/packman-common@{version}.zip` 下载。
- 版权归属 NVIDIA CORPORATION，使用 Apache License 2.0 许可。
