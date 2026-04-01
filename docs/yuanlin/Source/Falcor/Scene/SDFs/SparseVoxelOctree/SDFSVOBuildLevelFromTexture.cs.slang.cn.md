# SDFSVOBuildLevelFromTexture.cs.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SparseVoxelOctree/SDFSVOBuildLevelFromTexture.cs.slang`
> 类型: 计算着色器 (.cs.slang)
> 模块: Scene/SDFs/SparseVoxelOctree

## 功能概述

计算着色器,从 SDF 距离场纹理构建八叉树的各个层级。最细层级直接从纹理读取角点值并检测表面;其他层级检查子节点是否存在于哈希表中,若有子节点则创建父节点。

## 资源绑定

| 资源 | 类型 | 说明 |
|------|------|------|
| `gSDFGrid` | `Texture3D<float>` | SDF 距离场纹理 |
| `gVoxelHashTable` | `ParameterBlock<SDFSVOHashTable>` | 体素哈希表 |
| `gLocationCodes` | `RWByteAddressBuffer` | 位置码输出缓冲区 |
| `gVoxelCounts` | `RWByteAddressBuffer` | 每层级体素计数(非最细层级) |

## 条件编译宏

| 宏 | 说明 |
|----|------|
| `FINEST_LEVEL_PASS` | 1 = 最细层级构建,0 = 其他层级构建 |

## 依赖关系

### import
- `Scene.SDFs.SDFVoxelCommon`, `Scene.SDFs.SparseVoxelOctree.SDFSVOHashTable`

## 实现细节

- 最细层级:加载 8 个角点值,调用 `containsSurface` 检测表面,将有效体素插入哈希表
- 其他层级:为 8 个子节点创建位置码,查询哈希表确定哪些子节点存在,创建有效掩码
- 位置码高位设置有效标志(`1 << 31`)
- 非最细层级使用组共享内存累计组内体素数,通过原子操作更新全局计数
