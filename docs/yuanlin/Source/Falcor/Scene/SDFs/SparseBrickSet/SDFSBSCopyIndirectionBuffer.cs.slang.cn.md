# SDFSBSCopyIndirectionBuffer.cs.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SparseBrickSet/SDFSBSCopyIndirectionBuffer.cs.slang`
> 类型: 计算着色器 (.cs.slang)
> 模块: Scene/SDFs/SparseBrickSet

## 功能概述

计算着色器,将间接寻址缓冲区(线性 Buffer)的内容复制到间接寻址 3D 纹理中。这是因为前缀和操作在线性缓冲区上执行,但着色器需要从 3D 纹理进行查找。

## 资源绑定

| 资源 | 类型 | 说明 |
|------|------|------|
| `gParamBlock.virtualBricksPerAxis` | `uint` | 每轴虚拟砖块数 |
| `gParamBlock.indirectionBuffer` | `Buffer<uint>` | 源线性缓冲区 |
| `gParamBlock.indirectionTexture` | `RWTexture3D<uint>` | 目标 3D 纹理 |

## 实现细节

- 线程组大小 4x4x4
- 将 3D 坐标线性化索引到源缓冲区,写入目标纹理
