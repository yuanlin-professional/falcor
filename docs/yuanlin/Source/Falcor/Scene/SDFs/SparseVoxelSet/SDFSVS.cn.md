# SDFSVS 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SparseVoxelSet/SDFSVS.h`, `SDFSVS.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/SDFs/SparseVoxelSet

## 功能概述

`SDFSVS`(SDF 稀疏体素集)是 `SDFGrid` 的实现之一,创建仅包含与隐式表面重叠的体素的稀疏集合。每个体素生成一个 AABB,适用于 BLAS 中的程序化几何体。距离值归一化到 `[-1, 1]` 范围,其中 1 代表半个体素对角线。

## 类与接口

### `SDFSVS`

- **继承**: `SDFGrid`
- **职责**: 管理稀疏体素集的 GPU 资源创建和数据绑定

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<SDFSVS> create(ref<Device>)` | 工厂方法 |
| `size_t getSize() const` | 返回体素缓冲区和 AABB 缓冲区的总大小 |
| `void createResources(RenderContext*, bool)` | 两步构建:先统计表面体素数,再体素化 |
| `void bindShaderData(const ShaderVar&) const` | 绑定网格宽度、归一化因子、AABB 和体素缓冲区 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mValues` | `vector<int8_t>` | CPU 端归一化距离值 |
| `mpVoxelAABBBuffer` | `ref<Buffer>` | 体素 AABB 结构化缓冲区 |
| `mpVoxelBuffer` | `ref<Buffer>` | 体素数据结构化缓冲区(带 UAV 计数器) |
| `mVoxelCount` | `uint32_t` | 包含表面的体素数量 |

## 依赖关系

### 本文件引用
- `Scene/SDFs/SDFGrid.h`, `Core/API/Buffer.h`, `Core/API/Texture.h`, `Core/Pass/ComputePass.h`
- 计算着色器: `SDFSurfaceVoxelCounter.cs.slang`, `SDFSVSVoxelizer.cs.slang`

## 实现细节

- 构建过程分两步:
  1. 使用 `SDFSurfaceVoxelCounter` 统计包含表面的体素数
  2. 使用 `SDFSVSVoxelizer` 生成稀疏体素集和 AABB
- 归一化乘数 = `2 * gridWidth / sqrt(3)`
- 不支持从基元创建(仅支持从值数据构建)
- `deleteScratchData` 参数控制是否在构建后释放临时资源
