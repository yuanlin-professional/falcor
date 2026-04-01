# SDFSVOHashTable.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SparseVoxelOctree/SDFSVOHashTable.slang`
> 类型: Slang 着色器文件
> 模块: Scene/SDFs/SparseVoxelOctree

## 功能概述

实现 GPU 端的并发哈希表,用于 SDFSVO 构建阶段存储体素数据。使用位置码作为键,通过 64 位原子比较交换(NVAPI)实现无锁并发插入。采用开放地址法解决哈希冲突。

## 结构体与接口

### `SDFSVOHashTable`

#### 常量

| 常量 | 值 | 说明 |
|------|-----|------|
| `kEmptyLocationCode` | `uint2(0)` | 空位置码(用于检测空槽位) |
| `kVoxelByteSize` | 24 | 每个体素的字节大小 |

#### 成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `buffer` | `RWByteAddressBuffer` | 存储所有体素的缓冲区 |
| `capacity` | `uint` | 哈希表容量(必须为 2 的幂) |

#### 函数

| 方法签名 | 说明 |
|----------|------|
| `static uint hashLocationCode(uint2)` | 使用 Jenkins 哈希对位置码进行哈希 |
| `bool insert(uint2 locationCode, uint validMask, float4 v0, float4 v1, out uint slot)` | 插入体素(使用 NvInterlockedCompareExchangeUint64) |
| `bool setSVOOffset(uint2 locationCode, uint svoOffset)` | 设置体素的 SVO 偏移 |
| `bool lookup(uint2 locationCode, out uint svoOffset, out uint validMask, out uint2 packedValues)` | 查找体素并读取所有数据 |
| `bool contains(uint2 locationCode)` | 检查体素是否存在 |
| `static void write(...)` | 写入完整体素数据 |
| `static void writeValues(...)` | 仅写入值和有效掩码 |
| `static void writeSVOOffset(...)` | 仅写入 SVO 偏移 |
| `static uint2 readLocationCode(...)` | 读取位置码 |
| `static void readValues(...)` | 读取值数据 |

## 依赖关系

### include
- `Utils/NVAPI.slangh` (64 位原子操作)

### import
- `Scene.SDFs.SDFVoxelCommon`, `Utils.Math.HashUtils`

### 被以下文件引用
- `SDFSVOBuildLevelFromTexture.cs.slang`, `SDFSVOWriteSVOOffsets.cs.slang`, `SDFSVOBuildOctree.cs.slang`

## 实现细节

- 哈希函数使用 Jenkins 哈希的变体,对两个 32 位分量分别哈希后组合
- 插入使用 `NvInterlockedCompareExchangeUint64` 原子操作:
  - 空槽位(高位为 0):写入新体素
  - 已存在相同键:更新值(支持多次写入)
  - 其他键占用:线性探测下一个槽位
- 容量必须为 2 的幂,使用位掩码代替取模运算
- 位置码的最高位用作有效标志位
