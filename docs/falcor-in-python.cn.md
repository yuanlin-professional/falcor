### [Index](./index.md) | 在 Python 中使用 Falcor

--------

# 在 Python 中使用 Falcor

## 简介

Falcor 长期以来一直支持 Python 脚本。然而，脚本始终在内嵌的解释器中执行，形式为_渲染脚本_（创建渲染图、Mogwai 的高级脚本）或通过 `.pyscene` 文件进行场景构建。

最新版本的 Falcor 还可以通过 _Falcor Python 扩展_直接从 Python 中使用。这使得 Falcor 能够利用整个 Python 生态系统的能力，与 [NumPy](https://numpy.org/) 或 [PyTorch](https://pytorch.org/) 等流行框架相结合。当前的实现仍然相当有限，无法访问 Falcor 的所有功能，但已暴露了足够的 API 接口来支持初步的机器学习应用。

## Python 环境

要使用 _Falcor Python 扩展_，我们需要设置一个 Python 环境。我们建议使用 [Conda](https://docs.conda.io/)，因为它在 Windows 和 Linux 上都受支持，并且是管理 Python 虚拟环境的热门选择（尤其是在 Windows 上）。

要设置环境，请从以下链接下载最新的 Python 3.10 版本的 Miniconda：
https://docs.conda.io/projects/miniconda/en/latest/miniconda-other-installer-links.html

注意：Python 版本很重要。Falcor 附带了一个在构建过程中使用的预编译 Python 二进制文件。只要 Python 环境中使用相同的版本，就应该具有二进制兼容性。如果您想为不同的 Python 版本构建 Falcor，请将 `FALCOR_USE_SYSTEM_PYTHON` CMake 变量设置为 `ON`，并确保 CMake 能够找到要使用的 Python 二进制文件（即在已激活 Python 环境的 shell 中运行 CMake 配置）。

安装完成后，启动新安装的 Miniconda 终端。

要安装基本的 Falcor/NumPy/PyTorch 环境，您可以从本仓库根目录下的 [environment.yml](/environment.yml) 文件设置 `falcor-pytorch` 环境：

```
conda env create -f environment.yml
```

安装完成后，您可以使用以下命令_激活_ `falcor-pytorch` 环境：

```
conda activate falcor-pytorch
```

要使 _Falcor Python 扩展_在环境中可用，请切换到 Falcor 的二进制输出目录（即 `build/windows-ninja-msvc/bin/Release`），然后运行 `setpath.bat`（PowerShell 使用 `setpath.ps1`，Linux 使用 `setpath.sh`）。这将设置 Python 和二进制路径，以便加载扩展。

完成以上步骤后，您应该能够在 Python 解释器中成功加载 `falcor` 模块：

```
>>> import falcor
(Info) Loaded 49 plugin(s) in 0.19s
>>>
```

您也可以运行本文档末尾列出的[示例](#示例)。

## IDE（集成开发环境）支持

Falcor 构建系统会为 Python 扩展生成 _Python 接口存根文件_，其中包含所有导出的 Python 绑定的类型信息。这些类型信息可以实现代码补全等功能，大大改善开发体验。

我们建议使用 VS Code 编写 Python 代码，因为它是一个功能强大的 IDE，同时支持 Python 和 C++ 开发。为了让 VS Code 在我们在上一节中设置的 Python 环境中运行，最好从 Miniconda 终端中运行 `code` 来启动它。这将打开 VS Code，并自动设置好所有 Python/系统路径。

## 示例

以下是一些示例，展示了如何从 Python 中使用 Falcor 的基础知识：

- [scripts/python/balls/balls.py](/scripts/python/balls/balls.py)：
展示了如何从 Python 中使用计算着色器，并将一些移动的 2D 圆形渲染到屏幕上。
- [scripts/python/gaussian2d/gaussian2d.py](/scripts/python/gaussian2d/gaussian2d.py)：
展示了如何使用可微分 Slang 拟合 2D 高斯分布来表示 2D 图像。使用 Slang 实现了一个可微分的 2D 高斯渲染器，并利用 Slang 的自动微分来实现反向传播。该示例还展示了 PyTorch/CUDA 互操作，通过 PyTorch 运行优化循环。

## Agility SDK 限制

Falcor 使用 Microsoft Agility SDK 来访问 D3D12 的最新功能。要使 Agility SDK 在 Falcor Python 扩展中正常工作，请确保：
- 在 Windows 的"开发者选项"配置面板中启用"开发人员模式"。
- 确保 Python 解释器可执行文件与 Falcor Python 扩展安装在同一物理驱动器上。
