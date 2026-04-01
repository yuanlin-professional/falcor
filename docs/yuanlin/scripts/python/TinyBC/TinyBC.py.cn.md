# TinyBC.py 脚本文档

> 路径: `scripts/python/TinyBC/TinyBC.py`
> 类型: Python 脚本
> 模块: scripts/python/TinyBC

## 功能概述
该脚本实现了基于 Slang 的 BC7 Mode 6 纹理压缩器。它使用 Falcor 的计算通道（ComputePass）在 GPU 上执行纹理压缩，通过梯度下降优化压缩参数以最小化压缩误差。支持自定义优化步数、基准测试模式，并计算压缩后的 PSNR 质量指标。

## 主要功能/类

该脚本为顶层脚本，无类定义，直接执行压缩流程。

**命令行参数**:
| 参数 | 类型 | 说明 |
|------|------|------|
| input_path | `str` | 输入纹理路径（必需） |
| -o, --output_path | `str` | 输出解压纹理路径（可选） |
| -s, --opt_steps | `int` | 优化步数，默认 100 |
| -b, --benchmark | `flag` | 启用基准测试模式 |

**主要步骤**:
1. 创建 Falcor GPU 设备
2. 加载输入纹理（PIL → numpy → Falcor 纹理）
3. 创建输出解压纹理
4. 创建计算通道（`TinyBC.cs.slang`），配置 Adam 优化器参数
5. 执行压缩（以 4x4 块为单位）
6. 计算并输出 PSNR
7. 可选保存解压后的纹理

## 使用示例
```bash
# 基本压缩
python TinyBC.py input.png -o output.png

# 自定义优化步数
python TinyBC.py input.png -s 200 -o output.png

# 基准测试模式
python TinyBC.py input.png -b
```

## 依赖关系
### 导入的模块
- `sys` - 系统退出
- `time` - 性能计时
- `falcor` - Falcor 渲染框架（GPU 设备、纹理、计算通道）
- `argparse` - 命令行参数解析
- `numpy` - 数组运算和 PSNR 计算
- `PIL.Image` - 图像加载和保存
- `pathlib.Path` - 路径处理

## 实现细节
- 使用 Slang 着色器（`TinyBC.cs.slang`）实现 GPU 上的 BC7 Mode 6 编码
- 启用 Adam 优化器（`USE_ADAM=true`），学习率 0.1，beta1=0.9，beta2=0.999
- 压缩以 4x4 块为单位执行，线程组大小为 (w/4, h/4)
- 基准测试模式下执行 1000 次迭代取平均时间
- 输入格式为 RGBA32Float，支持任意尺寸（但宽高需为 4 的倍数）
- PSNR 计算：`20 * log10(1.0 / sqrt(MSE))`
