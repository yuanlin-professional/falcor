# Buffer 源码文档

> 路径: `Source/Falcor/Core/API/Buffer.h` / `Buffer.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

Buffer 类是 GPU 缓冲区资源的底层抽象，封装了缓冲区的创建、数据读写、内存映射和资源视图管理。支持原始缓冲区、类型化缓冲区和结构化缓冲区三种类型，以及设备本地内存、上传内存和回读内存三种内存类型。提供了与 Python（NumPy/PyTorch）的互操作功能。

## 类与接口

### `MemoryType`（枚举）
| 枚举值 | 说明 |
|--------|------|
| `DeviceLocal` | 设备本地内存，通过 `setBlob()` 更新 |
| `Upload` | 上传内存，可映射用于 CPU 写入 |
| `ReadBack` | 回读内存，可映射用于 CPU 读取 |

### `Buffer`
- **继承**: `Resource`
- **职责**: 管理 GPU 缓冲区资源的生命周期和访问

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `ref<ShaderResourceView> getSRV(uint64_t offset, uint64_t size)` | 获取着色器资源视图 |
| `ref<UnorderedAccessView> getUAV(uint64_t offset, uint64_t size)` | 获取无序访问视图 |
| `void setBlob(const void* pData, size_t offset, size_t size)` | 写入数据到缓冲区 |
| `void getBlob(void* pData, size_t offset, size_t size) const` | 从缓冲区读取数据 |
| `uint64_t getGpuAddress() const` | 获取 GPU 地址 |
| `size_t getSize() const` | 获取缓冲区字节大小 |
| `void* map() const` | 映射缓冲区到 CPU 可访问内存 |
| `void unmap() const` | 取消映射 |
| `template<T> void setElement(uint32_t index, const T& value)` | 写入单个元素 |
| `template<T> std::vector<T> getElements(...)` | 读取多个元素 |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `mGfxBufferResource` | `ComPtr<gfx::IBufferResource>` | GFX 缓冲区资源句柄 |
| `mMemoryType` | `MemoryType` | 内存类型 |
| `mElementCount` | `uint32_t` | 元素数量 |
| `mFormat` | `ResourceFormat` | 类型化缓冲区格式 |
| `mStructSize` | `uint32_t` | 结构化缓冲区的结构体大小 |
| `mpUAVCounter` | `ref<Buffer>` | UAV 计数器缓冲区（结构化缓冲区用） |

### `detail::FormatForElementType<T>`（模板特化）
- **职责**: C++ 类型到 ResourceFormat 的编译期映射，用于类型化缓冲区

## 实现细节

- 缓冲区大小限制在 4GB 以内（D3D12 限制）
- `setBlob` 根据内存类型选择不同路径：Upload 使用 map/memcpy、DeviceLocal 使用 RenderContext 更新
- 析构时通过 `Device::releaseResource` 进行延迟释放
- Python 绑定支持 `to_numpy`/`from_numpy` 以及 CUDA 环境下的 `to_torch`/`from_torch`

## 依赖关系
### 本文件引用
- `Resource.h`, `ResourceViews.h`, `Device.h`, `GFXAPI.h`, `GFXHelpers.h`
- `Core/Program/Program.h`, `Core/Program/ShaderVar.h`

### 被以下文件引用
- `CopyContext.h`, `ComputeContext.h`, `GpuMemoryHeap.h`, `VAO.h`, `ParameterBlock.h` 等
