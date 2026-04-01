# GridConverter 源码文档

> 路径: `Source/Falcor/Scene/Volume/GridConverter.h`
> 类型: C++ 头文件（含模板实现）
> 模块: Scene/Volume

## 功能概述

NanoVDB 体素网格到砖块纹理图集的转换器模板类。将 NanoVDB 的浮点网格数据转换为三层纹理结构（范围纹理 + 间接寻址纹理 + 图集纹理），支持 BC4 压缩（4 位）、UNORM8（8 位）和 UNORM16（16 位）三种精度。转换过程使用并行执行加速。

## 类与接口

### `NanoVDBToBricksConverter<TexelType, kBitsPerTexel>`

- **继承**: 无
- **职责**: 将 NanoVDB 浮点网格转换为砖块纹理图集。

#### 类型别名

| 别名 | 说明 |
|------|------|
| `NanoVDBConverterBC4` | BC4 压缩（4 位/纹素） |
| `NanoVDBConverterUNORM8` | UNORM8（8 位/纹素） |
| `NanoVDBConverterUNORM16` | UNORM16（16 位/纹素） |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `NanoVDBToBricksConverter(const nanovdb::FloatGrid* grid)` | 构造函数：计算砖块布局和图集尺寸 |
| `BrickedGrid convert(ref<Device> pDevice)` | 执行转换，返回 GPU 纹理 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpFloatGrid` | `const nanovdb::FloatGrid*` | 源 NanoVDB 网格 |
| `mAtlasSizeBricks` | `uint3` | 图集尺寸（砖块数） |
| `mLeafDim[4]` | `int3` | 4 级 mip 的叶节点维度 |
| `mRangeData` | `vector<uint32_t>` | 范围数据（majorant/minorant，RG16Float） |
| `mPtrData` | `vector<uint32_t>` | 间接寻址数据 |
| `mAtlasData` | `vector<TexelType>` | 图集体素数据 |
| `mNonEmptyCount` | `atomic_uint32_t` | 非空砖块计数 |

## 依赖关系

### 本文件引用

- `BrickedGrid.h` — 输出结构
- `BC4Encode.h` — BC4 压缩
- `Core/API/Device.h`, `Core/API/Formats.h` — GPU 设备和纹理格式
- `nanovdb/NanoVDB.h` — NanoVDB 数据结构
- `<execution>` — 并行执行策略

### 被以下文件引用

- `Grid.cpp` — 网格构建时使用

## 实现细节

- **砖块大小**: 固定为 8x8x8，与 NanoVDB 叶节点大小匹配。
- **图集布局**: 前两个维度为 2 的幂次（近似 `cbrt(leafCount)`），第三维度为余数。
- **范围数据**: 每砖块存储 majorant 和 minorant（RG16Float 格式），支持 4 级 mipmap 用于层次化 majorant 查询。
- **Halo 处理**: 每个砖块在计算 majorant/minorant 时考虑 1 体素的邻域（halo），确保三线性插值的正确性。
- **BC4 压缩**: 将体素值量化到 [0, 255]，每 4x4 块压缩为 8 字节。
- **并行转换**: 使用 `std::for_each(std::execution::par, ...)` 并行处理 Z 切片。
- 值归一化到 [minorant, majorant] 范围，majorant 向上取整到 fp16 精度以确保无损。
