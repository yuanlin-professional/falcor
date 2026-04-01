# ndarray 源码文档

> 路径: `Source/Falcor/Utils/Scripting/ndarray.h` + `Source/Falcor/Utils/Scripting/ndarray.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Scripting

## 功能概述

将 nanobind 的 `ndarray` 类回移植（backport）到 pybind11，实现基于 DLPack 协议的 N 维数组交换功能。该模块允许 Falcor 与 NumPy、PyTorch、TensorFlow、JAX 等 Python 数组编程框架进行零拷贝或自动转换的数据交换。支持设备类型识别（CPU、CUDA、Vulkan 等）、数据类型约束、形状约束、以及内存布局（C/Fortran 连续）约束。

## 类与接口

### `pybind11::ndarray<Args...>`

- **继承**: 无
- **职责**: 封装 DLPack 张量对象，提供类型安全的 N 维数组访问接口。

#### 模板参数

模板参数 `Args...` 可包含以下类型的任意组合：
- **标量类型**: `float`、`int32_t`、`uint8_t` 等 — 约束元素数据类型
- **形状**: `pybind11::shape<N, M, ...>` — 约束数组维度和各维大小（`pybind11::any` 表示任意大小）
- **内存布局**: `c_contig`、`f_contig`、`any_contig` — 约束内存排列顺序
- **设备类型**: `device::cpu`、`device::cuda` 等 — 约束数据所在设备
- **框架**: `numpy`、`pytorch`、`tensorflow`、`jax` — 指定目标框架

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `ndarray()` | 默认构造（空数组） |
| `ndarray(detail::ndarray_handle* handle)` | 从句柄构造，增加引用计数 |
| `ndarray(void* value, size_t ndim, const size_t* shape, ...)` | 从原始数据创建数组 |
| `dlpack::dtype dtype() const` | 返回数据类型描述 |
| `size_t ndim() const` | 返回维度数 |
| `size_t shape(size_t i) const` | 返回第 `i` 维的大小 |
| `int64_t stride(size_t i) const` | 返回第 `i` 维的步长 |
| `bool is_valid() const` | 返回数组是否有效 |
| `int32_t device_type() const` | 返回设备类型 |
| `int32_t device_id() const` | 返回设备 ID |
| `const Scalar* data() const` / `Scalar* data()` | 返回数据指针 |
| `auto& operator()(Ts... indices)` | 多维索引访问运算符（需指定标量类型和形状） |

### `pybind11::dlpack::dtype`

- **职责**: DLPack 数据类型描述符。

| 成员 | 类型 | 说明 |
|------|------|------|
| `code` | `uint8_t` | 类型代码（Int/UInt/Float/Bfloat/Complex） |
| `bits` | `uint8_t` | 每元素位数 |
| `lanes` | `uint16_t` | 向量通道数 |

### `pybind11::dlpack::dltensor`

- **职责**: DLPack 张量描述符。

| 成员 | 类型 | 说明 |
|------|------|------|
| `data` | `void*` | 数据指针 |
| `device` | `dlpack::device` | 设备信息 |
| `ndim` | `int32_t` | 维度数 |
| `dtype` | `dlpack::dtype` | 数据类型 |
| `shape` | `int64_t*` | 各维大小 |
| `strides` | `int64_t*` | 各维步长 |
| `byte_offset` | `uint64_t` | 字节偏移量 |

### `pybind11::dlpack::dtype_code`

| 枚举值 | 说明 |
|--------|------|
| `Int` | 有符号整数 |
| `UInt` | 无符号整数 |
| `Float` | 浮点数 |
| `Bfloat` | Brain 浮点数 |
| `Complex` | 复数 |

### 设备类型（`pybind11::device` 命名空间）

| 设备 | 值 | 说明 |
|------|----|------|
| `none` | 0 | 无设备 |
| `cpu` | 1 | CPU |
| `cuda` | 2 | CUDA GPU |
| `cuda_host` | 3 | CUDA 主机内存 |
| `opencl` | 4 | OpenCL |
| `vulkan` | 7 | Vulkan |
| `metal` | 8 | Metal |
| `rocm` | 10 | ROCm |
| `rocm_host` | 11 | ROCm 主机内存 |
| `cuda_managed` | 13 | CUDA 统一内存 |
| `oneapi` | 14 | OneAPI |

### 内部实现类型

| 类型 | 说明 |
|------|------|
| `detail::ndarray_handle` | 引用计数的数组句柄，管理 DLPack 张量的生命周期 |
| `detail::ndarray_req` | 数组需求描述，用于导入时校验类型/形状/设备/布局 |
| `detail::managed_dltensor` | 带删除器的 DLPack 张量封装 |
| `detail::nb_ndarray` | Python 对象类型，封装 ndarray_handle |

### 关键内部函数

| 函数 | 说明 |
|------|------|
| `ndarray_import` | 通过 DLPack 协议导入 Python 数组对象，校验约束条件 |
| `ndarray_create` | 从原始数据创建新的 ndarray 句柄 |
| `ndarray_inc_ref` / `ndarray_dec_ref` | 引用计数管理 |
| `ndarray_wrap` | 将 ndarray 句柄导出为指定框架的 Python 对象 |
| `dlpack_from_buffer_protocol` | 通过 Python 缓冲区协议创建 DLPack 胶囊 |

## 依赖关系

### 本文件引用

- `Core/Macros.h` — 导出宏（`FALCOR_API`）
- `<pybind11/pybind11.h>` — pybind11 核心
- `ScriptBindings.h` — 用于注册 `inspect_ndarray` 脚本绑定
- `<atomic>` — 原子引用计数

### 被以下文件引用

- 需要在 Python 和 C++ 之间交换 N 维数组数据的模块

## 实现细节

- 导入流程优先尝试调用对象的 `__dlpack__()` 方法，失败则尝试通过框架特定的 `to_dlpack` 函数，最后回退到 Python 缓冲区协议。
- 支持数据类型的隐式转换：当 dtype 或内存布局不匹配时，会尝试通过对应框架的转换方法（如 NumPy 的 `astype`、PyTorch 的 `to`）进行自动转换。
- 引用计数采用 `std::atomic<size_t>` 实现线程安全。
- DLPack 胶囊在消费后会被标记为 `used_dltensor`，防止重复消费。
- 注册了 `inspect_ndarray` Python 函数用于调试，可打印 ndarray 的详细信息（指针、维度、步长、设备、数据类型）。
- Python 缓冲区协议的支持覆盖了 Python 3.9 之前和之后的不同 API。
- 当数组没有显式步长信息时，默认假设 C 风格（行优先）内存布局。
