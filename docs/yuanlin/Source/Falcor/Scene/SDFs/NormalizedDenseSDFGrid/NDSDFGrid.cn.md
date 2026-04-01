# NDSDFGrid 源码文档

> 路径: `Source/Falcor/Scene/SDFs/NormalizedDenseSDFGrid/NDSDFGrid.h`, `NDSDFGrid.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/SDFs/NormalizedDenseSDFGrid

## 功能概述

`NDSDFGrid`(归一化密集有符号距离场网格)是 `SDFGrid` 的具体实现之一,创建一组密集层级体积纹理。距离值被归一化到 `[-1, 1]` 范围,其中 1 代表所选窄带厚度的一半体素对角线距离。使用 R8Snorm 格式存储,支持多级 LOD。

## 类与接口

### `NDSDFGrid`

- **继承**: `SDFGrid`
- **职责**: 管理归一化密集 SDF 网格的多级 LOD 纹理,提供光线追踪所需的 GPU 资源

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<NDSDFGrid> create(ref<Device>, float narrowBandThickness)` | 工厂方法,创建空的 NDSDFGrid 实例 |
| `size_t getSize() const` | 返回所有纹理和缓冲区的总字节大小 |
| `uint32_t getMaxPrimitiveIDBits() const` | 返回 LOD 数量所需的位数 |
| `Type getType() const` | 返回 `Type::NormalizedDenseGrid` |
| `void createResources(RenderContext*, bool)` | 创建或更新多级 LOD 3D 纹理(不支持从基元创建) |
| `const ref<Buffer>& getAABBBuffer() const` | 返回单位 AABB 缓冲区(共享) |
| `uint32_t getAABBCount() const` | 始终返回 1(整个网格使用单个 AABB) |
| `void bindShaderData(const ShaderVar&) const` | 绑定采样器、LOD 纹理数组和归一化参数 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mValues` | `vector<vector<int8_t>>` | 各 LOD 级别的归一化距离值(snorm8) |
| `mCoarsestLODGridWidth` | `uint32_t` | 最粗 LOD 的网格宽度 |
| `mCoarsestLODNormalizationFactor` | `float` | 最粗 LOD 的归一化因子 |
| `mNarrowBandThickness` | `float` | 窄带厚度参数 |
| `mNDSDFTextures` | `vector<ref<Texture>>` | 各级 LOD 的 3D 纹理 |
| `mpSharedData` | `shared_ptr<SharedData>` | 共享的采样器和单位 AABB 缓冲区 |

### `NDSDFGrid::SharedData`

- **职责**: 在所有 NDSDFGrid 实例间共享的采样器和 AABB 缓冲区

| 成员 | 类型 | 说明 |
|------|------|------|
| `pSampler` | `ref<Sampler>` | 线性过滤、Clamp 寻址的 3D 纹理采样器 |
| `pUnitAABBBuffer` | `ref<Buffer>` | 包含 `[-0.5, 0.5]` 单位 AABB 的缓冲区 |

## 依赖关系

### 本文件引用
- `Scene/SDFs/SDFGrid.h`, `Core/API/Texture.h`
- `Core/API/RenderContext.h`, `Utils/SharedCache.h`

### 被以下文件引用
- `SDFGrid.cpp` (工厂创建), `SDFGrid.slang` (着色器选择)

## 实现细节

- 最粗允许网格宽度为 8,LOD 数量由 `bitScanReverse(gridWidth / 8) + 1` 确定
- 归一化因子 = `0.5 * sqrt(3) * narrowBandThickness / gridWidth`
- 各 LOD 级别从原始值降采样(stride = `2^(lodCount - lod - 1)`)后归一化到 snorm8 格式
- 纹理宽度为 `(2^l) - 1` 形式(非标准 mip 级别),每级使用 R8Snorm 格式的 3D 纹理
- 使用 `SharedCache` 机制在多个实例间共享采样器和 AABB 缓冲区
