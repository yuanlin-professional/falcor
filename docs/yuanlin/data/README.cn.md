# data/ — 框架资源文件

## 功能概述

`data/` 目录存放 Falcor 渲染框架运行时所需的静态资源文件，包括蓝噪声纹理、GUI 字体和图标、内置网格模型以及单元测试用的参考图像。这些资源在框架初始化和测试执行时被自动加载。

## 目录清单

| 目录 | 说明 |
|------|------|
| `bluenoise/` | 蓝噪声纹理集合，用于随机采样的低差异序列 |
| `framework/` | 框架核心 GUI 资源 |
| `tests/` | 测试参考资源（纹理压缩格式测试图像） |

## 详细内容

### bluenoise/

蓝噪声纹理用于路径追踪等随机采样算法中的抖动和低差异采样。

| 子目录 | 说明 |
|--------|------|
| `2D/` | 2D 蓝噪声纹理（`bn_2D_0.png` ~ `bn_2D_52.png` 及 `bn_2D_128x128.png`），多通道独立蓝噪声序列 |
| `2Dx1D/` | 2D x 1D 联合蓝噪声纹理，用于时空联合采样 |

### framework/

框架 GUI 和内置资源。

| 子目录/文件 | 说明 |
|-------------|------|
| `fonts/` | GUI 字体文件 — `consolab.ttf`（Consolas 粗体）、`trebucbd.ttf`（Trebuchet 粗体）、DejaVu Sans Mono 14pt 位图字体（`.bin` + `.dds`） |
| `images/` | GUI 播放控件图标 — `play.jpg`、`pause.jpg`、`stop.jpg`、`rewind.jpg`、`next-frame.jpg`、`prev-frame.jpg`、`pass-icon.png` |
| `meshes/` | 内置网格模型 — `cube.obj`（立方体）、`sphere.fbx`（球体） |
| `nvidia.ico` | NVIDIA 图标文件（应用程序图标） |

### tests/

存放纹理压缩格式（Block Compression）的测试数据：各种 BC 格式（BC1-BC7、BC6H）的 `.dds` 源文件及对应的 `.png` 参考图像，用于纹理解码正确性验证。

| 格式 | 文件示例 | 说明 |
|------|----------|------|
| BC1 | `BC1Unorm.dds` / `BC1Unorm-ref.png` | 4:1 压缩 RGB |
| BC2 | `BC2Unorm.dds` / `BC2UnormSrgb.dds` | 显式 Alpha + RGB |
| BC3 | `BC3Unorm.dds` / `BC3UnormAlpha.dds` | 插值 Alpha + RGB |
| BC4 | `BC4Unorm.dds` | 单通道压缩 |
| BC5 | `BC5Unorm.dds` | 双通道压缩（法线贴图） |
| BC6H | `BC6HU16.dds` | HDR 纹理压缩 |
| BC7 | `BC7Unorm.dds` / `BC7UnormSrgb.dds` | 高质量 RGB/RGBA |
