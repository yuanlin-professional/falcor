# Utils (工具) 模块

## 功能概述

Utils 是 Falcor 渲染框架的通用工具子系统，提供框架各层共用的基础设施。该模块由根目录下的通用工具文件和 13 个功能子目录组成，涵盖以下核心领域：

- **日志系统**：分级日志输出（Fatal/Error/Warning/Info/Debug），支持控制台、文件和调试窗口多输出通道
- **属性系统**：基于 JSON 的类型安全属性存储，支持序列化/反序列化及 Python 字典互转
- **多线程**：全局线程池管理、线程屏障同步、CPU/GPU 混合任务调度
- **GPU 缓冲管理**：`BufferAllocator` 提供带对齐保证的 CPU-GPU 双缓冲内存分配
- **CUDA 互操作**：DX-CUDA 共享内存/信号量/纹理互操作
- **字符串工具**：前后缀检测、分割/合并、Base64 编解码、UTF 转换
- **通用容器**：类型擦除字典、对齐内存分配器、索引向量、共享缓存、分裂缓冲
- **路径解析**：文件搜索路径管理与解析
- **终端颜色**：终端输出着色支持
- **加密**：哈希/加密工具函数
- **Slang 工具**：着色器语言辅助宏与 NVAPI 绑定

## 子目录索引

| 子目录 | 说明 | 文档链接 |
|--------|------|----------|
| `Algorithm/` | 算法工具（并行归约、前缀和等 GPU 算法） | -- |
| `Color/` | 颜色空间转换、光谱采样与色彩科学工具 | -- |
| `Debug/` | 调试工具（GPU 调试输出、像素调试等） | -- |
| `Geometry/` | 几何工具（相交测试、包围盒等） | -- |
| `Image/` | 图像处理工具（图像IO、格式转换等） | -- |
| `Math/` | 数学库（向量、矩阵、四元数、噪声等） | -- |
| `SampleGenerators/` | 采样序列生成器（Halton、Sobol 等） | -- |
| `Sampling/` | 采样方法（半球采样、重要性采样等） | -- |
| `Scripting/` | Python 脚本绑定基础设施 | -- |
| `SDF/` | 有符号距离场工具 | -- |
| `Settings/` | 全局设置与属性过滤系统 | [Settings/README.md](Settings/README.md) |
| `Timing/` | 时钟、计时器与性能分析 | [Timing/README.md](Timing/README.md) |
| `UI/` | 用户界面与输入处理 | [UI/README.md](UI/README.md) |

## 根目录文件清单

| 文件名 | 类型 | 说明 |
|--------|------|------|
| `Logger.h` / `Logger.cpp` | 头文件/实现 | 分级日志系统，支持 Console/File/DebugWindow 输出，提供 `logDebug/Info/Warning/Error/Fatal` 便捷函数 |
| `Properties.h` / `Properties.cpp` | 头文件/实现 | 基于 JSON 的类型安全属性容器，支持枚举自动序列化、`serialize<Archive>` 检测及 Python 字典互转 |
| `Threading.h` / `Threading.cpp` | 头文件/实现 | 全局线程池（`Threading`）和线程屏障（`Barrier`） |
| `TaskManager.h` / `TaskManager.cpp` | 头文件/实现 | CPU/GPU 混合任务调度管理器，CPU 任务并行执行，GPU 任务顺序执行 |
| `BufferAllocator.h` / `BufferAllocator.cpp` | 头文件/实现 | GPU 缓冲区内存分配器，支持字节对齐与缓存行对齐 |
| `CudaUtils.h` / `CudaUtils.cpp` | 头文件/实现 | CUDA 互操作工具：设备内存管理、外部内存/信号量导入、纹理映射 |
| `CudaRuntime.h` | 头文件 | CUDA 运行时头文件封装，避免命名冲突 |
| `StringUtils.h` / `StringUtils.cpp` | 头文件/实现 | 字符串工具集：前后缀检测、分割/合并、Base64、URI 解码、字节大小格式化 |
| `CryptoUtils.h` / `CryptoUtils.cpp` | 头文件/实现 | 加密/哈希工具函数 |
| `PathResolving.h` / `PathResolving.cpp` | 头文件/实现 | 文件路径搜索与解析 |
| `TermColor.h` / `TermColor.cpp` | 头文件/实现 | 终端颜色输出支持 |
| `Dictionary.h` | 头文件 | 基于 `std::any` 的类型擦除键值字典 |
| `AlignedAllocator.h` | 头文件 | 对齐内存分配器模板 |
| `BinaryFileStream.h` | 头文件 | 二进制文件流读写 |
| `IndexedVector.h` | 头文件 | 带索引访问的向量容器 |
| `SharedCache.h` | 头文件 | 共享缓存容器 |
| `SplitBuffer.h` | 头文件 | 分裂缓冲区 |
| `NumericRange.h` | 头文件 | 数值范围工具 |
| `ObjectID.h` | 头文件 | 对象唯一标识符 |
| `ObjectIDPython.h` | 头文件 | ObjectID 的 Python 绑定 |
| `StringFormatters.h` | 头文件 | fmt 格式化器特化 |
| `fast_vector.h` | 头文件 | 轻量级小缓冲优化向量 |
| `HostDeviceShared.slangh` | Slang 头文件 | 主机-设备共享宏定义 |
| `Attributes.slang` | Slang 着色器 | 着色器端属性定义 |
| `SlangUtils.slang` | Slang 着色器 | Slang 着色器辅助函数 |
| `NVAPI.slang` / `NVAPI.slangh` | Slang 着色器/头文件 | NVAPI 着色器接口绑定 |

## 依赖关系

### 内部依赖
- `Core/Macros.h` -- 导出宏、断言宏、枚举运算符宏
- `Core/Error.h` -- 异常处理
- `Core/Enum.h` -- 枚举信息元编程（`Properties` 使用）
- `Core/Object.h` -- 引用计数基类（`CudaUtils` 使用）
- `Core/API/Buffer.h` / `Device.h` / `Fence.h` -- GPU 资源与同步（`BufferAllocator`、`CudaUtils` 使用）
- `Utils/Math/VectorTypes.h` -- 向量类型（`Properties` 使用）

### 外部依赖
- **nlohmann/json** -- JSON 库（`Properties` 底层存储）
- **pybind11** -- Python 绑定（`Properties` Python 互转）
- **fmt** -- 格式化字符串（`Logger`、`StringFormatters`）
- **CUDA / NVAPI** -- GPU 计算互操作（`CudaUtils`）
- **BS::thread_pool** -- 线程池库（`TaskManager`）

## 关键类与接口

### `Logger`
全局日志系统（静态类），支持 6 个严重级别与 3 个输出通道。提供模板化格式化辅助函数 `logDebug()`/`logInfo()`/`logWarning()`/`logError()`/`logFatal()`。

### `Properties`
类型安全的 JSON 属性容器，核心接口：
- `set<T>(name, value)` / `get<T>(name)` -- 类型安全读写
- `getTo<T>(name, value)` / `getOpt<T>(name)` -- 安全读取变体
- 自动检测 `serialize<Archive>` 成员函数进行深度序列化
- 支持枚举自动 string<->enum 转换

### `Threading`
全局线程池管理，`start()` 初始化（默认 16 线程），`dispatchTask()` 分发任务，`finish()` 等待完成，`shutdown()` 关闭。`Barrier` 提供线程屏障同步。

### `TaskManager`
CPU/GPU 混合任务调度器，特点：
- CPU 任务通过 `BS::thread_pool` 并行执行
- GPU 任务在 `finish()` 时顺序执行
- 异常安全，线程内异常可跨线程传播

### `BufferAllocator`
GPU 缓冲区内存管理器，维护 CPU 侧数据副本并按需同步到 GPU。支持最小字节对齐和缓存行对齐，脏区域追踪实现最小化上传。

### `Dictionary`
基于 `std::any` 的轻量级类型擦除字典，通过 `operator[]` 和 `getValue<T>()` 访问。

### `cuda_utils::CudaDevice` / `ExternalMemory` / `ExternalSemaphore`
CUDA 互操作封装：
- `CudaDevice` -- 在与 Falcor 相同适配器上创建 CUDA 设备/上下文/流
- `ExternalMemory` -- DX 缓冲区导入为 CUDA 可访问内存
- `ExternalSemaphore` -- DX Fence 导入为 CUDA 外部信号量，支持双向同步
