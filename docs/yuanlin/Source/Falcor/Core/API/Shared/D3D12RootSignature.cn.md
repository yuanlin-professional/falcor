# D3D12RootSignature 源码文档

> 路径: `Source/Falcor/Core/API/Shared/D3D12RootSignature.h` / `D3D12RootSignature.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API/Shared

## 功能概述

实现 D3D12 根签名（Root Signature）的封装。根签名定义了绑定到管线的资源布局，通过遍历程序的 ParameterBlock 层级结构来发现所有需要的根参数。根参数按以下顺序排列在根签名中：1) 描述符表；2) 根描述符；3) 根常量。支持从手动描述或程序反射信息自动创建根签名。

## 类与接口

### `D3D12RootSignature`
- **继承**: `Object`
- **职责**: 封装 D3D12 根签名的创建、管理和绑定，定义管线的资源绑定布局。

#### 类型别名

| 别名 | 实际类型 | 说明 |
|------|----------|------|
| `ApiHandle` | `ID3D12RootSignaturePtr` | D3D12 根签名智能指针 |
| `DescType` | `ShaderResourceType` | 描述符类型 |

#### 内部结构

##### `RootDescriptorDesc`
| 字段 | 类型 | 说明 |
|------|------|------|
| `type` | `DescType` | 描述符类型 |
| `regIndex` | `uint32_t` | 寄存器索引 |
| `spaceIndex` | `uint32_t` | 寄存器空间 |
| `visibility` | `ShaderVisibility` | 着色器可见性 |

##### `RootConstantsDesc`
| 字段 | 类型 | 说明 |
|------|------|------|
| `regIndex` | `uint32_t` | 寄存器索引 |
| `spaceIndex` | `uint32_t` | 寄存器空间 |
| `count` | `uint32_t` | 32 位常量的数量 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<D3D12RootSignature> create(ref<Device> pDevice, const Desc& desc)` | 从手动描述创建根签名 |
| `static ref<D3D12RootSignature> create(ref<Device> pDevice, const ProgramReflection* pReflection)` | 从程序反射信息自动创建根签名 |
| `const ApiHandle& getApiHandle() const` | 获取底层 D3D12 根签名句柄 |
| `size_t getDescriptorSetCount() const` | 获取描述符集数量 |
| `const D3D12DescriptorSetLayout& getDescriptorSet(size_t index) const` | 获取指定索引的描述符集布局 |
| `uint32_t getDescriptorSetBaseIndex() const` | 获取描述符表在根签名中的基础索引（始终为 0） |
| `uint32_t getRootDescriptorBaseIndex() const` | 获取根描述符在根签名中的基础索引 |
| `uint32_t getRootConstantBaseIndex() const` | 获取根常量在根签名中的基础索引 |
| `uint32_t getSizeInBytes() const` | 获取根签名的总字节大小 |
| `uint32_t getElementByteOffset(uint32_t elementIndex)` | 获取指定元素的字节偏移 |
| `void bindForGraphics(CopyContext* pCtx)` | 将根签名绑定到图形管线 |
| `void bindForCompute(CopyContext* pCtx)` | 将根签名绑定到计算管线 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | GPU 设备引用 |
| `mDesc` | `Desc` | 根签名描述 |
| `mApiHandle` | `ApiHandle` | D3D12 根签名原生句柄 |
| `mSizeInBytes` | `uint32_t` | 根签名总字节大小 |
| `mElementByteOffset` | `std::vector<uint32_t>` | 各元素的字节偏移表 |

### `D3D12RootSignature::Desc`
- **职责**: 根签名的配置描述，支持添加描述符集、根描述符和根常量。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `Desc& addDescriptorSet(const D3D12DescriptorSetLayout& setLayout)` | 添加描述符集（描述符表） |
| `Desc& addRootDescriptor(DescType type, uint32_t regIndex, uint32_t spaceIndex, ShaderVisibility visibility)` | 添加根描述符 |
| `Desc& addRootConstants(uint32_t regIndex, uint32_t spaceIndex, uint32_t count)` | 添加根常量 |
| `size_t getSetsCount() const` | 获取描述符集数量 |
| `size_t getRootDescriptorCount() const` | 获取根描述符数量 |
| `size_t getRootConstantCount() const` | 获取根常量数量 |

### 辅助函数与结构

| 函数/结构 | 说明 |
|-----------|------|
| `RootSignatureParams` | 内部结构，保存 D3D12 根参数向量、描述符范围和大小信息 |
| `D3D12_SHADER_VISIBILITY getShaderVisibility(ShaderVisibility)` | 将 Falcor 着色器可见性转换为 D3D12 着色器可见性 |
| `D3D12_DESCRIPTOR_RANGE_TYPE getRootDescRangeType(DescType)` | 将着色器资源类型转换为 D3D12 描述符范围类型 |
| `void initD3D12RootParams(const Desc&, RootSignatureParams&)` | 初始化 D3D12 根参数数组 |
| `void addParamBlockSets(const ParameterBlockReflection*, Desc&)` | 递归遍历 ParameterBlock 添加描述符集布局 |
| `void addRootDescriptors(const ParameterBlockReflection*, Desc&)` | 递归遍历 ParameterBlock 添加根描述符 |

## 依赖关系

### 本文件引用
- `D3D12Handles.h` — COM 智能指针
- `D3D12DescriptorSet.h` — 描述符集和绑定使用枚举
- `Core/Macros.h` / `Core/Object.h` — 框架基础
- `Core/API/ShaderResourceType.h` — 着色器资源类型
- `Core/API/Device.h` — GPU 设备
- `Core/API/GFXAPI.h` — GFX 抽象层
- `Core/API/NativeHandleTraits.h` — 原生句柄转换
- `Core/Program/ProgramReflection.h` — 程序反射信息
- `Utils/Math/Common.h` — 数学工具（`isPowerOf2`）

### 被以下文件引用
- `D3D12DescriptorSet.h` — 在绑定方法中使用根签名

## 实现细节

- **根参数排列顺序**: 描述符表排在最前（索引从 0 开始），接着是根描述符，最后是根常量。根常量放在最后是为了避免地址对齐填充问题（地址必须 8 字节对齐，但根常量可能有奇数个）。
- **大小限制**: D3D12 根签名限制为 64 DWORD（256 字节），描述符表占 1 DWORD（实际为 8 字节），根描述符占 2 DWORD，根常量每个 1 DWORD。若超出限制会抛出异常。
- **从反射创建**: `create(pDevice, pReflection)` 通过递归遍历程序反射的默认 ParameterBlock，收集所有描述符集布局和根描述符信息，自动构建根签名描述。
- **着色器可见性映射**: D3D12 不支持组合可见性标志，只能指定 `ALL` 或单一着色器阶段。若 Falcor 可见性包含多个阶段，则映射为 `D3D12_SHADER_VISIBILITY_ALL`。
- **版本**: 使用 Root Signature Version 1.1 进行序列化，启用 `D3D12_ROOT_SIGNATURE_FLAG_ALLOW_INPUT_ASSEMBLER_INPUT_LAYOUT` 标志以支持输入装配器。
- **绑定**: 通过获取命令列表的原生句柄，调用 `SetGraphicsRootSignature` 或 `SetComputeRootSignature` 完成绑定。
