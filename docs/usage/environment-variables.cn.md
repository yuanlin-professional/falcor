### [Index](../index.md) | [Usage](./index.md) | 环境变量

--------

# 环境变量

Falcor 中使用以下环境变量：

| 变量名 | 描述 |
|-----|-----|
| `FALCOR_DEVMODE` | 设置为 `1` 以启用开发模式。在开发模式下，shader 和数据文件从 `Source` 文件夹而非二进制输出目录加载，从而支持 shader 热重载（`F5`）。注意，从 Visual Studio 启动任何 Falcor 项目时，此环境变量默认已设置。 |
| `FALCOR_MEDIA_FOLDERS` | 指定以分号（`;`）分隔的绝对路径列表，这些路径包含 Falcor 场景。使用相对路径名加载场景时，Falcor 会在这些路径中搜索。 |
