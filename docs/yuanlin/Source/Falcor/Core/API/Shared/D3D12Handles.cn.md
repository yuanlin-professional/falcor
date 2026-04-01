# D3D12Handles 源码文档

> 路径: `Source/Falcor/Core/API/Shared/D3D12Handles.h`
> 类型: C++ 头文件
> 模块: Core/API/Shared

## 功能概述

定义 D3D12 COM 接口的智能指针类型别名。使用 `_COM_SMARTPTR_TYPEDEF` 宏为常用的 D3D12 COM 接口生成带引用计数的智能指针类型，简化 D3D12 资源的生命周期管理。所有定义仅在 `FALCOR_HAS_D3D12` 宏启用时有效。

## 宏定义

| 宏 | 说明 |
|-----|------|
| `FALCOR_MAKE_SMART_COM_PTR(_a)` | 为指定的 COM 接口类型 `_a` 生成对应的智能指针类型 `_aPtr` |

## 生成的智能指针类型

| 智能指针类型 | 对应 COM 接口 | 说明 |
|-------------|--------------|------|
| `ID3DBlobPtr` | `ID3DBlob` | 二进制大对象（用于着色器编译结果、根签名序列化等） |
| `ID3D12DescriptorHeapPtr` | `ID3D12DescriptorHeap` | D3D12 描述符堆 |
| `ID3D12RootSignaturePtr` | `ID3D12RootSignature` | D3D12 根签名 |

## 依赖关系

### 本文件引用
- `<d3d12.h>` — D3D12 API 头文件
- `<comdef.h>` — COM 智能指针工具宏

### 被以下文件引用
- `D3D12DescriptorHeap.h` — 使用 `ID3D12DescriptorHeapPtr`
- `D3D12DescriptorPool.h` — 间接使用（通过 `D3D12Handles.h`）
- `D3D12RootSignature.h` — 使用 `ID3D12RootSignaturePtr` 和 `ID3DBlobPtr`

## 实现细节

- 所有智能指针类型定义被 `#if FALCOR_HAS_D3D12` 条件编译保护，在非 D3D12 后端（如 Vulkan）上不会生成这些类型。
- `FALCOR_MAKE_SMART_COM_PTR` 宏展开为 `_COM_SMARTPTR_TYPEDEF(_a, __uuidof(_a))`，利用 MSVC 特定的 `_com_ptr_t` 模板实现自动引用计数管理。
