# SDFSBSAssignBrickValidityFromSDFieldPass.cs.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SparseBrickSet/SDFSBSAssignBrickValidityFromSDFieldPass.cs.slang`
> 类型: 计算着色器 (.cs.slang)
> 模块: Scene/SDFs/SparseBrickSet

## 功能概述

计算着色器,从已有距离场纹理中检测砖块的有效性。遍历每个体素,检测是否包含隐式表面,然后标记对应砖块为有效。支持两种模式:组级别砖块创建(每组一个砖块)和体素级别原子操作。

## 资源绑定

| 资源 | 类型 | 说明 |
|------|------|------|
| `gParamBlock.sdfGrid` | `Texture3D<float>` | SDF 距离场纹理 |
| `gParamBlock.brickValidity` | `RWByteAddressBuffer` | 砖块有效性缓冲区(输出) |

## 条件编译宏

| 宏 | 说明 |
|----|------|
| `GROUP_BRICK_CREATION` | 使用组级别砖块创建模式(组共享内存) |
| `GROUP_WIDTH` | 线程组宽度 |

## 依赖关系

### import
- `Scene.SDFs.SDFVoxelCommon`
