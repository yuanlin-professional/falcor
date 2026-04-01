# ShaderResourceType 源码文档

> 路径: `Source/Falcor/Core/API/ShaderResourceType.h`
> 类型: C++ 头文件
> 模块: Core/API

## 功能概述

定义了着色器资源类型枚举，用于标识着色器中不同类型的资源绑定。

## 类与接口

### `ShaderResourceType`（枚举）
| 值 | 说明 |
|----|------|
| `TextureSrv` | 纹理 SRV |
| `TextureUav` | 纹理 UAV |
| `RawBufferSrv` | 原始缓冲区 SRV |
| `RawBufferUav` | 原始缓冲区 UAV |
| `TypedBufferSrv` | 类型化缓冲区 SRV |
| `TypedBufferUav` | 类型化缓冲区 UAV |
| `Cbv` | 常量缓冲区视图 |
| `StructuredBufferUav` | 结构化缓冲区 UAV |
| `StructuredBufferSrv` | 结构化缓冲区 SRV |
| `AccelerationStructureSrv` | 加速结构 SRV |
| `Dsv` | 深度-模板视图 |
| `Rtv` | 渲染目标视图 |
| `Sampler` | 采样器 |
| `Count` | 枚举总数 |

## 依赖关系
### 被以下文件引用
- 描述符管理和资源绑定相关模块
