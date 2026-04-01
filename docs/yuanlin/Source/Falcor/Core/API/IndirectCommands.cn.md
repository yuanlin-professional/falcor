# IndirectCommands 源码文档

> 路径: `Source/Falcor/Core/API/IndirectCommands.h`
> 类型: C++ 头文件
> 模块: Core/API

## 功能概述

定义了间接绘制和间接调度命令的参数结构体，与 GPU 端间接参数缓冲区的内存布局一致。

## 结构体

### `DispatchArguments`
| 成员 | 类型 | 说明 |
|------|------|------|
| `ThreadGroupCountX` | `uint32_t` | X 维度线程组数量 |
| `ThreadGroupCountY` | `uint32_t` | Y 维度线程组数量 |
| `ThreadGroupCountZ` | `uint32_t` | Z 维度线程组数量 |

### `DrawArguments`
| 成员 | 类型 | 说明 |
|------|------|------|
| `VertexCountPerInstance` | `uint32_t` | 每实例顶点数 |
| `InstanceCount` | `uint32_t` | 实例数量 |
| `StartVertexLocation` | `uint32_t` | 起始顶点偏移 |
| `StartInstanceLocation` | `uint32_t` | 起始实例偏移 |

### `DrawIndexedArguments`
| 成员 | 类型 | 说明 |
|------|------|------|
| `IndexCountPerInstance` | `uint32_t` | 每实例索引数 |
| `InstanceCount` | `uint32_t` | 实例数量 |
| `StartIndexLocation` | `uint32_t` | 起始索引偏移 |
| `BaseVertexLocation` | `int32_t` | 基础顶点偏移 |
| `StartInstanceLocation` | `uint32_t` | 起始实例偏移 |

## 依赖关系
### 被以下文件引用
- 间接绘制/调度相关代码
