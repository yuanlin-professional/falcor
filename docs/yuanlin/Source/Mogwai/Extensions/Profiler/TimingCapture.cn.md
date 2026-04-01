# TimingCapture 源码文档

> 路径: `Source/Mogwai/Extensions/Profiler/TimingCapture.h`, `Source/Mogwai/Extensions/Profiler/TimingCapture.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Mogwai / Extensions / Profiler

## 功能概述

`TimingCapture` 是 Mogwai 的帧时间捕获扩展，用于将每帧的渲染时间记录到文件中。启动捕获后，每帧开始时会将上一帧的帧时间追加写入指定文件。适用于性能分析和基准测试场景。

## 类与接口

### `TimingCapture`

- **继承**: `Extension`
- **职责**: 将帧渲染时间逐帧记录到文本文件中，供性能分析使用。通过 `MOGWAI_EXTENSION` 宏自动注册为 Mogwai 扩展。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static UniquePtr create(Renderer* pRenderer)` | 工厂方法，创建 `TimingCapture` 实例 |
| `void beginFrame(RenderContext*, const ref<Fbo>&) override` | 每帧开始时调用 `recordPreviousFrameTime()` 记录上一帧时间 |
| `void registerScriptBindings(pybind11::module& m) override` | 注册 Python 绑定：`captureFrameTime(path)` 方法 |
| `std::string getScriptVar() const override` | 返回脚本变量名 `"timingCapture"` |
| `void captureFrameTime(std::filesystem::path path)` | 开始/停止帧时间捕获。传入非空路径开始捕获（覆盖写入），传入空路径停止捕获 |
| `void recordPreviousFrameTime()` | 将上一帧的帧时间写入文件（跳过第一帧，因为第一帧无有效时间数据） |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mFrameTimeFile` | `std::ofstream` | 帧时间输出文件流，打开时表示正在捕获 |

## Python 脚本接口

| 方法 | 说明 |
|------|------|
| `m.timingCapture.captureFrameTime(path)` | 开始将帧时间记录到指定文件。传入空路径停止记录 |

## 依赖关系

### 本文件引用

- `../../Mogwai.h` — Renderer 和 Extension 基类
- `Falcor.h` — Falcor 框架核心
- `<fstream>` — 文件输出流

### 被以下文件引用

- 通过 `MOGWAI_EXTENSION(TimingCapture)` 宏自动注册，被渲染器在启动时发现和加载
- `Source/Mogwai/MogwaiScripting.cpp` — 通过名称查找并注册为弃用全局变量 `tc`

## 实现细节

- **自动注册**: 通过 `MOGWAI_EXTENSION(TimingCapture)` 宏在静态初始化阶段自动注册。
- **覆盖写入**: 使用 `std::ofstream::trunc` 模式打开文件，会覆盖已有内容。若文件已存在则输出警告。
- **帧时间来源**: 通过 `mpRenderer->getFrameRate().getLastFrameTime()` 获取上一帧的帧时间。`FrameRate` 对象在每帧开始时更新，因此首个有效时间数据从第二帧开始可用。
- **输出格式**: 每帧时间占一行，为浮点数（单位：秒），以 `std::endl` 结尾确保立即刷新。
- **错误处理**: 文件打开失败时输出错误日志并继续运行（不中断程序）。
- **停止方式**: 调用 `captureFrameTime("")` 传入空路径即可关闭文件并停止捕获。
