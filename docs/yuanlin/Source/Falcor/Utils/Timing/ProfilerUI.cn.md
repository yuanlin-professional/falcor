# ProfilerUI 源码文档

> 路径: `Source/Falcor/Utils/Timing/ProfilerUI.h` + `Source/Falcor/Utils/Timing/ProfilerUI.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Timing

## 功能概述

性能分析器的可视化 UI 组件，使用 ImGui 直接绘制。提供事件列表表格（显示 CPU/GPU 时间和百分比）、堆叠柱状图、鼠标高亮交互、性能捕获控制等功能。支持 CPU 时间和 GPU 时间两种图表模式，以及 EMA 平均值切换。

## 类与接口

### `ProfilerUI`

- **导出宏**: `FALCOR_API`
- **职责**: 渲染 Profiler 的可视化界面

#### 枚举

| 枚举 `GraphMode` | 说明 |
|-------------------|------|
| `Off` | 不显示图表 |
| `CpuTime` | 显示 CPU 时间图表 |
| `GpuTime` | 显示 GPU 时间图表 |
| `Count` | 枚举计数 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `ProfilerUI(Profiler* pProfiler)` | 构造函数，绑定 Profiler 实例 |
| `void render()` | 渲染完整的 Profiler UI，必须在有效的 ImGui 窗口中调用 |

#### 私有方法

| 方法签名 | 说明 |
|----------|------|
| `void renderOptions()` | 渲染选项区域（暂停、平均值切换、图表模式、捕获控制） |
| `void renderGraph(const ImVec2& size, size_t highlightIndex, size_t& newHighlightIndex)` | 渲染堆叠柱状时间线图表 |
| `void updateEventData()` | 从 Profiler 事件数据更新内部事件信息 |
| `void updateGraphData()` | 更新图表数据，写入环形历史缓冲区 |
| `void clearGraphData()` | 清空图表历史数据 |

#### 内部结构 `EventData`

| 成员 | 类型 | 说明 |
|------|------|------|
| `pEvent` | `Profiler::Event*` | 源 Profiler 事件指针 |
| `name` | `std::string` | 事件短名称（去除路径前缀） |
| `level` | `uint32_t` | 事件树层级深度 |
| `color` | `uint32_t` | 调色板颜色 |
| `mutedColor` | `uint32_t` | 低不透明度颜色（用于背景） |
| `cpuTime` | `float` | 当前 CPU 时间 |
| `gpuTime` | `float` | 当前 GPU 时间 |
| `graphValue` | `float` | 当前图表值 |
| `maxGraphValue` | `float` | 历史中的最大图表值 |
| `graphHistory` | `std::vector<float>` | 图表值环形历史（256 帧） |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpProfiler` | `Profiler*` | Profiler 实例指针 |
| `mGraphMode` | `GraphMode` | 当前图表模式 |
| `mEnableAverage` | `bool` | 是否使用 EMA 平均值 |
| `mEventData` | `std::vector<EventData>` | 预处理的事件数据（跨帧持久化） |
| `mTotalCpuTime` | `float` | 顶层事件 CPU 总时间 |
| `mTotalGpuTime` | `float` | 顶层事件 GPU 总时间 |
| `mHistoryLength` | `size_t` | 图表历史当前长度 |
| `mHistoryWrite` | `size_t` | 图表历史写入索引 |
| `mHighlightIndex` | `size_t` | 当前高亮事件索引 |

## 依赖关系

### 本文件引用

- `Profiler.h` — Profiler 类及其 Event/Stats 类型
- `Core/Macros.h` — 宏定义
- `Core/Platform/OS.h` — 文件对话框
- `imgui.h` — ImGui 直接 API

### 被以下文件引用

- 应用框架主窗口中嵌入 Profiler 面板

## 实现细节

- **五列表格**: 使用 ImGui Columns 布局，显示事件名称、CPU 时间、CPU 百分比条、GPU 时间、GPU 百分比条，第六列为可选图表区域。
- **色盲友好调色板**: 使用 12 色调色板（避免黄色系），事件按注册顺序循环分配颜色。
- **堆叠柱状图**: 每帧一根柱体（宽 2px），从右到左绘制历史数据。事件按层级堆叠，顶层事件纵向排列，子事件嵌套在父事件内。
- **鼠标交互**: 鼠标悬停在百分比条或图表柱体上时高亮对应事件，显示工具提示（名称和百分比/统计信息）。
- **捕获功能**: 选项区域提供 Start/End Capture 按钮，结束时弹出文件保存对话框将数据导出为 JSON。
- **暂停时冻结**: Profiler 暂停时图表历史不再更新写入索引，画面冻结在最后的状态。
