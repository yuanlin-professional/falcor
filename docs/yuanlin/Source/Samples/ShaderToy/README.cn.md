# ShaderToy -- ShaderToy着色器示例

## 功能概述

本示例展示了如何在 Falcor 框架中使用全屏后处理 Pass（`FullScreenPass`）运行类似 ShaderToy 风格的程序化着色器。程序不加载任何 3D 场景或纹理，而是将分辨率和全局时间作为参数传递给像素着色器，完全在 GPU 上实时生成画面。

着色器 `Toy.ps.slang` 实现了一个**血液细胞（Red Blood Cell）动画效果**：
- 在屏幕上以网格分布随机大小的圆形"细胞"。
- 每个细胞使用基于多项式的颜色映射函数 `getRBCColor()` 从暗红到亮红渐变。
- 细胞中心随时间做正弦/余弦抖动动画，模拟流动效果。
- 使用伪随机函数 `rand()` / `rand2()` 生成位置和大小的变化。

C++ 侧创建了完整的渲染状态（光栅化状态、深度模板状态、混合状态、纹理采样器），但实际渲染仅依赖 `FullScreenPass` 执行全屏像素着色。

## 文件清单

| 文件名 | 类型 | 说明 |
|---|---|---|
| `ShaderToy.cpp` | C++ 源文件 | 应用主逻辑，创建 FullScreenPass，每帧传递分辨率和时间参数 |
| `ShaderToy.h` | C++ 头文件 | `ShaderToy` 类声明，包含采样器、渲染状态和 FullScreenPass 成员 |
| `Toy.ps.slang` | Slang 着色器 | 像素着色器，实现程序化血液细胞动画效果 |
| `CMakeLists.txt` | 构建脚本 | CMake 配置 |

## 依赖关系

- `ShaderToy.cpp` 包含 `ShaderToy.h`，并在运行时加载 `Toy.ps.slang` 着色器。
- `ShaderToy.h` 依赖 `Falcor.h`、`Core/SampleApp` 和 `Core/Pass/FullScreenPass`。
- 着色器 `Toy.ps.slang` 是独立的，不导入额外的 Slang 模块，仅使用常量缓冲区 `ToyCB` 接收 CPU 侧参数。
