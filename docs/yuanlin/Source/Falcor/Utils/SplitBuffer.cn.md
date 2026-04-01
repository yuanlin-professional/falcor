# SplitBuffer.h 源码文档

> 路径: `Source/Falcor/Utils/SplitBuffer.h`
> 类型: C++ 头文件（仅头文件）
> 模块: Utils

## 功能概述

提供跨越 GPU 4GB 缓冲区限制的分片缓冲区实现。GPU 当前最大支持 4GB 缓冲区，当需要存储更多数据时，必须拆分为多个缓冲区。`SplitBuffer` 使用 32 位索引的高位选择缓冲区、低位选择元素，最多支持 2^32 个元素。

## 类与接口

### `SplitBuffer<T, TByteBuffer>`

- **继承**: 无
- **模板约束**: `sizeof(T)` 必须为 2 的幂
- **模板参数**:
  - `T` — 存储的元素类型
  - `TByteBuffer` — 是否使用字节缓冲区模式
- **职责**: 管理多个 CPU/GPU 缓冲区，自动负载均衡和索引编码

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `size_t getMaxBufferCount() const` | 获取最大缓冲区数量 |
| `void setBufferCount(uint32_t count)` | 强制设置缓冲区数量 |
| `void setName(string_view name)` | 设置缓冲区名称 |
| `void setBufferCountDefinePrefix(string prefix)` | 设置 GPU 端缓冲区数量宏前缀 |
| `uint32_t insert(Iter first, Iter last)` | 插入元素范围，返回编码后的索引 |
| `uint32_t insertEmpty(size_t itemCount)` | 插入空白范围 |
| `void createGpuBuffers(const ref<Device>&, ResourceBindFlags)` | 创建 GPU 缓冲区 |
| `bool empty() const` | 是否为空 |
| `size_t getBufferCount() const` | 获取缓冲区数量 |
| `size_t getByteSize() const` | 获取总字节数 |
| `const T& operator[](uint32_t index) const` | 通过编码索引访问 CPU 数据 |
| `void dropCpuData()` | 释放 CPU 数据以节省内存 |
| `ref<Buffer> getGpuBuffer(uint32_t bufferIndex) const` | 获取指定 GPU 缓冲区 |
| `uint64_t getGpuAddress(uint32_t index) const` | 获取编码索引对应的 GPU 地址 |
| `void getShaderDefines(DefineList& defines) const` | 设置着色器宏定义 |
| `void bindShaderData(const ShaderVar& var) const` | 绑定到着色器变量 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mCpuBuffers` | `vector<vector<T>>` | CPU 端多缓冲区 |
| `mGpuBuffers` | `vector<ref<Buffer>>` | GPU 端多缓冲区 |
| `kBufferSizeLimit` | `constexpr size_t` | 单个缓冲区大小上限 |
| `kMaxBufferCount` | `constexpr size_t` | 最大缓冲区数量 |

## 依赖关系

### 本文件引用

- `Core/API/Buffer.h`、`Core/API/Device.h` — GPU 缓冲区和设备
- `Core/Program/DefineList.h`、`Core/Program/ShaderVar.h` — 着色器集成
- `Core/Error.h` — 错误检查

### 被以下文件引用

- 场景几何数据存储（顶点缓冲区、索引缓冲区等）
- `SceneCache` — 友元类

## 实现细节

- 索引编码：高 `kBufferIndexBits` 位为缓冲区索引，低 `kElementIndexBits` 位为元素索引
- 插入时自动选择元素最少的缓冲区进行负载均衡
- 当缓冲区满时自动创建新缓冲区
- 小于 16 字节或字节缓冲区模式使用 2GB 上限，否则使用 4GB 上限
- GPU 端使用结构化缓冲区数组
