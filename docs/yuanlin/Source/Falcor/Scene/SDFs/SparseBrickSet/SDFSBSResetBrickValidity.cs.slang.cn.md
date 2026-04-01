# SDFSBSResetBrickValidity.cs.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SparseBrickSet/SDFSBSResetBrickValidity.cs.slang`
> 类型: 计算着色器 (.cs.slang)
> 模块: Scene/SDFs/SparseBrickSet

## 功能概述

计算着色器,根据砖块有效性标志重置间接寻址缓冲区。将无效砖块的间接寻址条目设置为 `UINT32_MAX`,使其在后续查找时被识别为无效。

## 资源绑定

| 资源 | 类型 | 说明 |
|------|------|------|
| `gParamBlock.virtualBrickCount` | `uint` | 虚拟砖块总数 |
| `gParamBlock.brickValidity` | `Buffer<uint>` | 砖块有效性标志(由前缀和产生) |
| `gParamBlock.indirectionBuffer` | `RWBuffer<uint>` | 间接寻址缓冲区 |

## 依赖关系

### include
- `Utils/Math/MathConstants.slangh`

## 实现细节

- 线程组大小 256x1x1
- 仅对无效砖块(validity == 0)写入 `UINT32_MAX`,有效砖块保持不变
