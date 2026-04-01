# balls.py 脚本文档

> 路径: `scripts/python/balls/balls.py`
> 类型: Python 脚本
> 模块: scripts/python/balls

## 功能概述
该脚本是一个使用 Falcor 计算着色器的简单物理模拟示例。它模拟 100 个弹跳球的运动，使用两个计算通道分别更新球的物理状态和渲染球到纹理。这是一个展示 Falcor Python API 中计算通道（ComputePass）、结构化缓冲区（StructuredBuffer）和纹理操作的入门示例。

## 主要功能/类

该脚本为顶层脚本，直接执行模拟循环。

**常量配置**:
| 常量 | 值 | 说明 |
|------|-----|------|
| BALL_COUNT | 100 | 球的数量 |
| BALL_RADIUS | 0.1 | 球的半径 |
| RESOLUTION | 1024 | 渲染分辨率 |

**主要步骤**:
1. 创建带窗口的测试平台（1024x1024）
2. 创建结构化缓冲区存储球的位置和速度（每球 16 字节：float2 位置 + float2 速度）
3. 创建渲染纹理（RGBA32Float）
4. 创建两个计算通道：
   - `balls_update.cs.slang`：更新球的物理状态
   - `balls_render.cs.slang`：渲染球到纹理
5. 用随机值初始化球
6. 主循环：计算帧间时间 → 更新物理 → 渲染 → 显示

## 使用示例
```bash
python balls.py
# 打开窗口显示弹跳球动画
```

## 依赖关系
### 导入的模块
- `falcor` - Falcor 渲染框架
- `pathlib.Path` - 路径处理
- `time` - 帧间时间计算
- `numpy` - 随机数据生成

## 实现细节
- 球数据存储在单个结构化缓冲区中，每个元素 16 字节（float2 位置 + float2 速度）
- 更新通道使用 `BALL_COUNT` 个线程，每线程处理一个球
- 渲染通道使用 `RESOLUTION x RESOLUTION` 个线程，每线程处理一个像素
- 使用 `device.profiler.event()` 进行 GPU 性能分析
- 初始位置和速度为 [-1, 1] 范围的随机值
- 隐藏 Falcor UI（`testbed.show_ui = False`）以获得干净的可视化
