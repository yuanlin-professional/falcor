# NvApiExDesc 源码文档

> 路径: `Source/Falcor/Core/API/NvApiExDesc.h`
> 类型: C++ 头文件
> 模块: Core/API

## 功能概述

NvApiExDesc.h 封装了 NVAPI PSO（管线状态对象）扩展描述符，用于在 D3D12 管线创建时设置 NVIDIA 特有的扩展功能，如自定义语义和着色器扩展槽。仅在 `FALCOR_HAS_NVAPI` 宏启用时有效。

## 类与接口

### `NvApiPsoExDesc`（结构体）
| 成员 | 类型 | 说明 |
|------|------|------|
| `psoExtension` | `NV_PSO_EXTENSION` | PSO 扩展类型 |
| `mVsExDesc` | `NVAPI_D3D12_PSO_VERTEX_SHADER_DESC` | 顶点着色器扩展描述 |
| `mHsExDesc` | `NVAPI_D3D12_PSO_HULL_SHADER_DESC` | 外壳着色器扩展描述 |
| `mDsExDesc` | `NVAPI_D3D12_PSO_DOMAIN_SHADER_DESC` | 域着色器扩展描述 |
| `mGsExDesc` | `NVAPI_D3D12_PSO_GEOMETRY_SHADER_DESC` | 几何着色器扩展描述 |
| `mExtSlotDesc` | `NVAPI_D3D12_PSO_SET_SHADER_EXTENSION_SLOT_DESC` | 着色器扩展槽描述 |
| `mCustomSemantics` | `vector<NV_CUSTOM_SEMANTIC>` | 自定义语义列表 |

### 辅助函数
| 函数 | 说明 |
|------|------|
| `createNvApiVsExDesc(NvApiPsoExDesc&)` | 创建顶点着色器扩展描述（NV_X_RIGHT、NV_VIEWPORT_MASK 语义） |
| `createNvApiUavSlotExDesc(NvApiPsoExDesc&, uint32_t)` | 创建 UAV 扩展槽描述 |
| `findNvApiShaderRegister(const ref<const ProgramKernels>&)` | 查找 `g_NvidiaExt` 寄存器索引 |

## 依赖关系
### 本文件引用
- `Core/Macros.h`, `Core/Error.h`, `Core/Program/ProgramVersion.h`
- NVAPI 头文件（`nvapi.h`）
