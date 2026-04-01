### [Index](../index.md) | [Development](./index.md) | CMake

--------

# CMake

Falcor 使用 [CMake](https://cmake.org) 构建系统。关于 CMake 的入门介绍可参阅 [An Introduction to Modern CMake](https://cliutils.gitlab.io/modern-cmake)。

## 向现有可执行文件/库添加源文件

要向现有的可执行文件/库目标添加新的源文件或 shader 文件，只需在相应 `CMakeLists.txt` 文件的 `target_sources` 调用中列出这些文件即可。请尽量保持文件列表按字母顺序排列。

Shader 文件将根据文件扩展名自动识别，并在构建过程中复制到输出目录（前提是相应的 `CMakeLists.txt` 中有 `target_copy_shaders` 调用）。

注意：修改 `CMakeLists.txt` 文件后，下次构建时会检测到更改并重新运行 CMake 生成器。使用由 CMake 生成的 Visual Studio 解决方案时，构建后会弹出一个对话框，允许重新加载解决方案以反映对解决方案文件所做的更改。

## 创建新的示例应用程序

运行 `tools/make_new_sample_app.bat <Name>` 来创建新的示例应用程序并调整构建文件。

## 创建新的渲染通道

运行 `tools/make_new_render_pass.bat <Name>` 来创建新的渲染通道库并调整构建文件。

## Falcor 特有的 CMake 函数

CMake 脚本中提供了一些 Falcor 特有的函数：

**`target_copy_shaders(<name> <output_dir>)`**

设置构建规则，将目标的所有 shader 复制到输出目录。
指定的 `output_dir` 相对于全局 shader 输出目录（`FALCOR_SHADER_OUTPUT_DIRECTORY`）。

**`target_copy_data_folder(<target>)`**

设置构建后规则，将目标的数据文件夹复制到输出目录。

**`add_falcor_executable(<target>)`**

创建 Falcor 应用程序并链接 Falcor 主库。

**`add_renderpass(<target>)`**

创建 Falcor 渲染通道。

**`target_source_group(<target> <folder>)`**

用于为 Visual Studio 创建源文件组的辅助函数。
此函数将目标的所有源文件添加到指定文件夹的源文件组中。
