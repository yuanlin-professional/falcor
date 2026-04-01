![](docs/images/teaser.png)

# Falcor

Falcor 是一个支持 DirectX 12 和 Vulkan 的实时渲染框架。它旨在提高研究和原型项目的开发效率。

主要特性包括：
* 对许多常见图形操作的抽象，例如 shader 编译、模型加载和场景渲染
* 光线追踪支持
* Python 脚本支持
* 渲染图系统，用于构建模块化渲染器
* 常见渲染技术，例如后处理效果
* 无偏路径追踪器
* 集成多种 RTX SDK，如 DLSS、RTXDI 和 NRD

## 前置条件
- Windows 10 版本 20H2（2020 年 10 月更新）或更新版本，操作系统构建修订版 .789 或更新
- Visual Studio 2022
- [Windows 10 SDK (10.0.19041.0) for Windows 10, version 2004](https://developer.microsoft.com/en-us/windows/downloads/windows-10-sdk/)
- 支持 DirectX Raytracing 的 GPU，例如 NVIDIA Titan V 或 GeForce RTX
- NVIDIA 驱动程序 466.11 或更新版本

可选项：
- Windows 10 Graphics Tools。要在启用调试层的情况下运行 DirectX 12 应用程序，必须安装此工具。有两种安装方式：
    - 点击 Windows 按钮并输入 `Optional Features`，在打开的窗口中点击 `Add a feature` 并选择 `Graphics Tools`。
    - 从[此处](https://docs.microsoft.com/en-us/windows-hardware/test/hlk/windows-hardware-lab-kit#supplemental-content-for-graphics-media-and-mean-time-between-failures-mtbf-tests)下载离线安装包。选择与你使用的操作系统版本匹配的 ZIP 文件（不是用于构建 Falcor 的 SDK 版本）。ZIP 文件中包含一份说明如何安装 Graphics Tools 的文档。
- NVAPI、CUDA、OptiX（见下文）

## 构建 Falcor
Falcor 使用 [CMake](https://cmake.org) 构建系统。有关如何将 Falcor 与 CMake 结合使用的更多信息，请参阅 [CMake](docs/development/cmake.md) 开发文档页面。

### Visual Studio
如果你使用 Visual Studio 2022，可以在克隆此仓库后运行 `setup_vs2022.bat` 来设置原生 Visual Studio 解决方案。解决方案文件将写入 `build/windows-vs2022`，二进制输出位于 `build/windows-vs2022/bin`。

### Visual Studio Code
如果你使用 Visual Studio Code，请在克隆此仓库后运行 `setup.bat`。这将在 `.vscode` 文件夹中设置一个具有合理默认配置的 VS Code 工作区（仅在 `.vscode` 尚不存在时）。在 VS Code 中打开项目文件夹时，它会提示安装推荐的扩展。我们建议你安装，但至少确保安装了 _CMake Tools_。要构建 Falcor，你可以通过执行 _CMake: Select Configure Preset_ 操作（Ctrl+Shift+P）来选择配置预设。选择 _Windows Ninja/MSVC_ 预设，然后只需点击 _Build_（或按 F7）即可构建项目。二进制输出位于 `build/windows-ninja-msvc/bin`。

警告：不要从 _Git Bash_ 启动 VS Code，它会将 `PATH` 环境变量修改为不兼容的格式，导致 CMake 出现问题。

### Linux
Falcor 对 Ubuntu 22.04 有实验性支持。要在 Linux 上构建 Falcor，请在克隆此仓库后运行 `setup.sh`。你还需要使用以下命令安装一些系统库头文件：

```
sudo apt install xorg-dev libgtk-3-dev
```

你可以使用上面 _Visual Studio Code_ 部分中描述的相同说明来构建 Falcor，只需选择 _Linux/GCC_ 预设即可。

### 配置预设
Falcor 使用存储在 `CMakePresets.json` 中的 _CMake Presets_ 来提供一组常用的构建配置。你可以运行 `cmake --list-presets` 来获取所有可用配置预设的完整列表：

```
$ cmake --list-presets
Available configure presets:

  "windows-vs2022"           - Windows VS2022
  "windows-ninja-msvc"       - Windows Ninja/MSVC
  "linux-clang"              - Linux Ninja/Clang
  "linux-gcc"                - Linux Ninja/GCC
```

使用 `cmake --preset <preset name>` 为给定预设生成构建树。构建树将写入 `build/<preset name>` 文件夹，二进制输出文件位于 `build/<preset name>/bin`。

可以使用 `cmake --build build/<preset name>` 编译现有的构建树。

## 在 Python 中使用 Falcor
有关如何将 Falcor 作为 Python 模块使用的更多信息，请参阅 [Falcor In Python](docs/falcor-in-python.md)。

## Microsoft DirectX 12 Agility SDK
Falcor 使用 [Microsoft DirectX 12 Agility SDK](https://devblogs.microsoft.com/directx/directx12agility/) 来获取最新的 DirectX 12 特性。应用程序可以通过在主 `.cpp` 文件中添加 `FALCOR_EXPORT_D3D12_AGILITY_SDK` 来启用 Agility SDK。`Mogwai`、`FalcorTest` 和 `RenderGraphEditor` 默认启用了 Agility SDK。

## NVAPI
要启用 NVAPI 支持，请前往 https://developer.nvidia.com/nvapi 下载最新版本的 NVAPI（此构建已针对版本 R535 进行测试）。
将 ZIP 文件的内容解压到 `external/packman/` 并将 `R535-developer` 重命名为 `nvapi`。

## NSight Aftermath
要启用 NSight Aftermath 支持，请前往 https://developer.nvidia.com/nsight-aftermath 下载最新版本的 Aftermath（此构建已针对版本 2023.1 进行测试）。
将 ZIP 文件的内容解压到 `external/packman/aftermath`。

## CUDA
要启用 CUDA 支持，请下载并安装 [CUDA 11.6.2](https://developer.nvidia.com/cuda-11-6-2-download-archive) 或更高版本，然后重新配置构建。

有关如何使用 CUDA 的示例，请参阅位于 `Source/Samples/CudaInterop` 的 `CudaInterop` 示例应用程序。

## OptiX
如果你想使用 Falcor 的 OptiX 功能（特别是 `OptixDenoiser` 渲染通道），请下载 [OptiX SDK](https://developer.nvidia.com/designworks/optix/download)（Falcor 目前针对 OptiX 版本 7.3 进行测试）。运行安装程序后，将 OptiX SDK 文件夹链接或复制到 `external/packman/optix`（即文件 `external/packman/optix/include/optix.h` 应存在）。

注意：你还需要安装 CUDA 才能编译 `OptixDenoiser` 渲染通道，详情见上文。

## NVIDIA RTX SDK
Falcor 附带以下 NVIDIA RTX SDK：

- DLSS (https://github.com/NVIDIA/DLSS)
- RTXDI (https://github.com/NVIDIAGameWorks/RTXDI)
- NRD (https://github.com/NVIDIAGameWorks/RayTracingDenoiser)

请注意，这些 SDK 与 Falcor 不在同一许可证下，详情请参阅 [LICENSE.md](LICENSE.md)。

## 资源
- [Falcor](https://github.com/NVIDIAGameWorks/Falcor)：Falcor 的 GitHub 页面。
- [文档](./docs/index.md)：附加信息和教程。
    - [入门指南](./docs/getting-started.md)
    - [渲染图教程](./docs/tutorials/index.md)
- [Rendering Resources](https://benedikt-bitterli.me/resources)：可在 Falcor 中加载的场景集合（pbrt-v4 格式）。
- [ORCA](https://developer.nvidia.com/orca)：为 Falcor 优化的场景和资产集合。
- [Slang](https://github.com/shader-slang/slang)：Falcor 的着色语言和编译器。

## 引用
如果你在研究项目中使用 Falcor 并发表了相关论文，请引用本项目。
BibTeX 条目如下：

```bibtex
@Misc{Kallweit22,
   author =      {Simon Kallweit and Petrik Clarberg and Craig Kolb and Tom{'a}{\v s} Davidovi{\v c} and Kai-Hwa Yao and Theresa Foley and Yong He and Lifan Wu and Lucy Chen and Tomas Akenine-M{\"o}ller and Chris Wyman and Cyril Crassin and Nir Benty},
   title =       {The {Falcor} Rendering Framework},
   year =        {2022},
   month =       {8},
   url =         {https://github.com/NVIDIAGameWorks/Falcor},
   note =        {\url{https://github.com/NVIDIAGameWorks/Falcor}}
}
```
