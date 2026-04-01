# PixelDebug 源码文档
> 路径: `Source/Falcor/Utils/Debug/PixelDebug.h` + `Source/Falcor/Utils/Debug/PixelDebug.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Debug

## 功能概述

提供像素级着色器调试工具，允许用户在着色器中使用 `print()` 和 `assert()` 函数进行调试。主机端（CPU）负责管理 GPU 缓冲区、异步回读数据并在 UI 中显示结果。用户可通过鼠标左键点击选择要调试的像素，选中像素的 `print()` 输出会被记录并显示，而 `assert()` 会记录所有失败断言的像素坐标。

调试功能关闭时，着色器中的调试代码通过宏被完全移除，不产生性能开销。启用时使用异步回读，性能影响较小。

## 类与接口

### `PixelDebug`

- **继承**: 无
- **职责**: 管理像素调试的完整生命周期，包括 GPU 缓冲区分配、着色器程序配置、数据异步回读、UI 渲染和鼠标交互。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `PixelDebug(ref<Device> pDevice, uint32_t printCapacity = 100, uint32_t assertCapacity = 100)` | 构造函数。指定 GPU 设备和 print/assert 缓冲区容量。 |
| `void beginFrame(RenderContext* pRenderContext, const uint2& frameDim)` | 帧开始时调用。清空上一帧数据，若启用则分配/清零 GPU 缓冲区。 |
| `void endFrame(RenderContext* pRenderContext)` | 帧结束时调用。将 GPU 数据复制到回读缓冲区，提交命令并插入 Fence 信号。 |
| `void prepareProgram(const ref<Program>& pProgram, const ShaderVar& var)` | 在着色器程序执行前调用。启用时添加 `_PIXEL_DEBUG_ENABLED` 宏定义并绑定缓冲区资源；禁用时移除该宏。同时收集程序反射器中的哈希字符串映射。 |
| `void renderUI(Gui::Widgets* widget = nullptr)` | 渲染调试 UI。显示启用开关、像素选择控件以及打印/断言日志。同时将新数据输出到日志。 |
| `bool onMouseEvent(const MouseEvent& mouseEvent)` | 处理鼠标事件。左键点击时更新选中像素坐标。 |
| `void enable()` | 启用调试功能。 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | GPU 设备引用 |
| `mpCounterBuffer` | `ref<Buffer>` | GPU 上的计数器缓冲区（print 计数 + assert 计数） |
| `mpPrintBuffer` | `ref<Buffer>` | GPU 上的 print 记录结构化缓冲区 |
| `mpAssertBuffer` | `ref<Buffer>` | GPU 上的 assert 记录结构化缓冲区 |
| `mpReadbackBuffer` | `ref<Buffer>` | 用于异步回读的暂存缓冲区（包含以上三个缓冲区的数据） |
| `mpFence` | `ref<Fence>` | GPU 围栏，用于同步回读操作 |
| `mEnabled` | `bool` | 是否启用调试功能 |
| `mSelectedPixel` | `uint2` | 当前选中的像素坐标 |
| `mHashToString` | `unordered_map<uint32_t, string>` | 字符串哈希到字符串值的映射表，用于还原 print 消息 |
| `mPrintData` | `vector<PrintRecord>` | 从 GPU 回读的 print 数据 |
| `mAssertData` | `vector<AssertRecord>` | 从 GPU 回读的 assert 数据 |
| `mPrintCapacity` | `const uint32_t` | print 缓冲区容量（元素数） |
| `mAssertCapacity` | `const uint32_t` | assert 缓冲区容量（元素数） |

## 依赖关系

### 本文件引用

- `PixelDebugTypes.slang` — 定义 `PrintRecord`、`AssertRecord`、`PrintValueType` 等共享类型
- `Core/Macros.h` — Falcor 宏定义
- `Core/API/Buffer.h` — GPU 缓冲区
- `Core/API/Fence.h` — GPU 围栏
- `Core/API/Device.h` — GPU 设备
- `Core/API/RenderContext.h` — 渲染上下文
- `Core/Program/Program.h` — 着色器程序
- `Core/Program/ShaderVar.h` — 着色器变量绑定
- `Utils/Logger.h` — 日志输出
- `Utils/UI/Gui.h` — UI 控件
- `Utils/UI/InputTypes.h` — 输入事件类型
- `<fstd/bit.h>` — 位转换工具（`bit_cast`）

### 被以下文件引用

- 各渲染通道（RenderPass）中需要像素调试功能的模块

## 实现细节

- **缓冲区布局**: 回读缓冲区按 `[计数器 | print数据 | assert数据]` 的顺序排列，一次性复制所有数据。
- **异步回读**: 使用 `Fence` 实现 GPU→CPU 的异步数据传输。`endFrame()` 提交复制命令并插入信号，`copyDataToCPU()` 中等待信号后读取数据。
- **数据解析**: print 数据根据 `PrintValueType` 枚举解析为 `bool`、`int`、`uint` 或 `float`，支持向量类型（最多 4 分量）。
- **字符串还原**: 着色器中的字符串通过哈希传递，主机端从程序反射器获取哈希→字符串映射表进行还原。
- **宏控制**: 通过 `_PIXEL_DEBUG_ENABLED` 宏定义控制着色器端调试代码的编译，禁用时不产生额外开销。
- **PrintRecord 对齐**: 静态断言确保 `PrintRecord` 大小为 16 字节的整数倍。
