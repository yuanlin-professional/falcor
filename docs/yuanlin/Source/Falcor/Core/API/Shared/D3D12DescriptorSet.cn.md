# D3D12DescriptorSet 源码文档

> 路径: `Source/Falcor/Core/API/Shared/D3D12DescriptorSet.h` / `D3D12DescriptorSet.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API/Shared

## 功能概述

实现 D3D12 描述符集（Descriptor Set），对应 D3D12 中描述符表（Descriptor Table）的概念。描述符集从描述符池中分配一段连续的描述符空间，支持设置 SRV、UAV、CBV、采样器等各类描述符，并可绑定到图形或计算管线。该类是 Falcor 资源绑定系统的核心组件之一。

## 类与接口

### `D3D12DescriptorSetBindingUsage`（枚举）
- **职责**: 指定描述符集的绑定方式。

| 枚举值 | 说明 |
|--------|------|
| `ExplicitBind` | 默认方式，必须通过 `bindForGraphics` 或 `bindForCompute` 显式绑定 |
| `RootSignatureOffset` | 通过根签名偏移隐式绑定，不允许调用显式绑定方法 |

### `D3D12DescriptorSet`
- **继承**: `Object`
- **职责**: 管理一组连续的 D3D12 描述符，支持按范围索引设置各类资源视图，并将描述符表绑定到 GPU 管线。

#### 类型别名

| 别名 | 实际类型 | 说明 |
|------|----------|------|
| `Type` | `ShaderResourceType` | 着色器资源类型 |
| `CpuHandle` | `D3D12_CPU_DESCRIPTOR_HANDLE` | CPU 描述符句柄 |
| `GpuHandle` | `D3D12_GPU_DESCRIPTOR_HANDLE` | GPU 描述符句柄 |
| `ApiData` | `DescriptorSetApiData` | 平台相关内部数据 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<D3D12DescriptorSet> create(ref<Device>, ref<D3D12DescriptorPool>, const D3D12DescriptorSetLayout&)` | 从指定描述符池创建描述符集 |
| `static ref<D3D12DescriptorSet> create(ref<Device>, const D3D12DescriptorSetLayout&, D3D12DescriptorSetBindingUsage)` | 创建描述符集并指定绑定方式（自动选择 CPU 或 GPU 描述符池） |
| `CpuHandle getCpuHandle(uint32_t rangeIndex, uint32_t descInRange) const` | 获取指定范围和描述符索引的 CPU 句柄 |
| `GpuHandle getGpuHandle(uint32_t rangeIndex, uint32_t descInRange) const` | 获取 GPU 句柄（当前未支持，会抛出异常） |
| `void setCpuHandle(uint32_t rangeIndex, uint32_t descIndex, const CpuHandle& handle)` | 通过 CPU 句柄复制设置描述符 |
| `void setSrv(uint32_t rangeIndex, uint32_t descIndex, const ShaderResourceView* pSrv)` | 设置着色器资源视图 |
| `void setUav(uint32_t rangeIndex, uint32_t descIndex, const UnorderedAccessView* pUav)` | 设置无序访问视图 |
| `void setSampler(uint32_t rangeIndex, uint32_t descIndex, const Sampler* pSampler)` | 设置采样器 |
| `void setCbv(uint32_t rangeIndex, uint32_t descIndex, D3D12ConstantBufferView* pView)` | 设置常量缓冲区视图 |
| `void bindForGraphics(CopyContext* pCtx, const D3D12RootSignature* pRootSig, uint32_t rootIndex)` | 将描述符集绑定到图形管线的指定根参数索引 |
| `void bindForCompute(CopyContext* pCtx, const D3D12RootSignature* pRootSig, uint32_t rootIndex)` | 将描述符集绑定到计算管线的指定根参数索引 |
| `size_t getRangeCount() const` | 获取布局中的范围数量 |
| `const D3D12DescriptorSetLayout::Range& getRange(uint32_t range) const` | 获取指定索引的范围定义 |
| `ShaderVisibility getVisibility() const` | 获取着色器可见性 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | GPU 设备引用 |
| `mLayout` | `D3D12DescriptorSetLayout` | 描述符集布局 |
| `mpApiData` | `std::shared_ptr<ApiData>` | 平台相关数据（堆分配和范围偏移） |
| `mpPool` | `ref<D3D12DescriptorPool>` | 所属描述符池 |

### 辅助函数

| 函数签名 | 说明 |
|----------|------|
| `static D3D12DescriptorHeap* getHeap(const D3D12DescriptorPool* pPool, D3D12DescriptorSet::Type type)` | 根据资源类型从池中获取对应的描述符堆 |
| `static D3D12_GPU_DESCRIPTOR_HANDLE copyDescriptorTableToGPU(CopyContext*, DescriptorSetApiData*, const D3D12DescriptorSetLayout&)` | 将 CPU 描述符表复制到 GPU 可见的临时描述符堆 |

## 依赖关系

### 本文件引用
- `D3D12DescriptorSetLayout.h` — 描述符集布局定义
- `D3D12DescriptorPool.h` — 描述符池
- `D3D12DescriptorData.h` — 内部数据结构
- `D3D12ConstantBufferView.h` — 常量缓冲区视图
- `Core/Macros.h` / `Core/Object.h` — 框架基础
- `Core/API/Device.h` — GPU 设备
- `Core/API/CopyContext.h` — 命令上下文
- `Core/API/GFXAPI.h` — GFX 抽象层接口
- `Core/API/NativeHandleTraits.h` — 原生句柄转换
- `Core/API/ShaderResourceType.h` — 着色器资源类型

### 被以下文件引用
- `D3D12ConstantBufferView.h` / `.cpp` — 内部使用描述符集存储 CBV
- `D3D12RootSignature.h` — 在绑定操作中使用

## 实现细节

- **分配策略**: 构造时将布局中所有范围的描述符数量累加，从对应类型的描述符堆中分配一个连续块。所有范围的描述符必须属于同一种 D3D12 堆类型。若分配失败，会先执行延迟释放再重试一次。
- **绑定机制（GFX 模式）**: 绑定时要求描述符集分配在 CPU 堆上（非着色器可见），通过 `copyDescriptorTableToGPU` 将 CPU 描述符复制到 GFX 管理的临时 GPU 可见描述符堆中，然后调用 `SetGraphicsRootDescriptorTable` 或 `SetComputeRootDescriptorTable` 完成绑定。
- **RootSignatureOffset 模式**: 使用该绑定方式创建的描述符集会从 GPU 描述符池分配，通过根签名偏移直接引用，无需显式绑定。
- **析构**: 析构时将内部 API 数据提交给描述符池的延迟释放队列，等待 GPU 完成使用后回收。
