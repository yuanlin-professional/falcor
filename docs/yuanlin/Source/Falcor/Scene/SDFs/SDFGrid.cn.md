# SDFGrid 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SDFGrid.h`, `Source/Falcor/Scene/SDFs/SDFGrid.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/SDFs

## 功能概述

`SDFGrid` 是有符号距离场(SDF)网格的基类,定义了所有 SDF 网格实现的通用接口和数据管理功能。SDF 网格将距离值存储在网格单元/体素角点处,其局部空间为 `[-0.5, 0.5]^3`。

SDF 网格实现创建 AABB(轴对齐包围盒)用作程序化图元构建加速结构。SDF 网格不支持光栅化渲染,必须构建到加速结构中,然后通过交叉着色器或内联交叉测试进行光线追踪。

系统提供四种 SDF 网格实现:
1. **归一化密集网格 (NDSDFGrid)** - 密集层级体积纹理
2. **稀疏体素集 (SDFSVS)** - 稀疏体素集合
3. **稀疏砖块集 (SDFSBS)** - NxN×N 砖块的稀疏集合,支持有损压缩
4. **稀疏体素八叉树 (SDFSVO)** - 稀疏体素八叉树结构

## 类与接口

### `SDFGrid`

- **继承**: `Object`
- **职责**: 提供 SDF 网格的公共接口,管理基元数据、网格值的加载/保存,以及 GPU 资源创建的抽象接口

#### 内部枚举

| 枚举 | 说明 |
|------|------|
| `Type` | SDF 网格类型枚举: `None`, `NormalizedDenseGrid`, `SparseVoxelSet`, `SparseBrickSet`, `SparseVoxelOctree` |
| `UpdateFlags` | 更新标志: `None`, `AABBsChanged`(AABB 变化需重建 BLAS), `BuffersReallocated`(缓冲区重新分配需重新绑定) |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `uint32_t setPrimitives(const vector<SDF3DPrimitive>&, uint32_t gridWidth)` | 设置用于构建 SDF 网格的基元,返回第一个基元的 ID |
| `uint32_t addPrimitives(const vector<SDF3DPrimitive>&)` | 添加基元到现有集合,返回起始基元 ID |
| `void removePrimitives(const vector<uint32_t>& primitiveIDs)` | 按 ID 移除基元(已烘焙的基元不可移除) |
| `void updatePrimitives(const vector<pair<uint32_t, SDF3DPrimitive>>&)` | 更新指定 ID 的基元数据 |
| `void setValues(const vector<float>&, uint32_t gridWidth)` | 设置网格角点的有符号距离值 |
| `bool loadValuesFromFile(const path&)` | 从 `.sdfg` 文件加载距离值 |
| `void generateCheeseValues(uint32_t gridWidth, uint32_t seed)` | 生成类似瑞士奶酪形状的距离值 |
| `bool writeValuesFromPrimitivesToFile(const path&, RenderContext*)` | 将基元评估为网格值并写入文件 |
| `uint32_t loadPrimitivesFromFile(const path&, uint32_t gridWidth)` | 从 JSON 文件加载基元 |
| `bool writePrimitivesToFile(const path&)` | 将基元写入 JSON 文件 |
| `virtual UpdateFlags update(RenderContext*)` | 更新 SDF 网格并应用变更(虚函数) |
| `virtual size_t getSize() const = 0` | 返回 SDF 网格的字节大小(纯虚函数) |
| `virtual uint32_t getMaxPrimitiveIDBits() const = 0` | 返回 HitInfo 中基元 ID 字段的最大位数 |
| `virtual Type getType() const = 0` | 返回 SDF 网格类型 |
| `virtual void createResources(RenderContext*, bool) = 0` | 创建 GPU 渲染所需的数据结构 |
| `virtual const ref<Buffer>& getAABBBuffer() const = 0` | 返回可用于创建加速结构的 AABB 缓冲区 |
| `virtual uint32_t getAABBCount() const = 0` | 返回 AABB 数量 |
| `virtual void bindShaderData(const ShaderVar&) const = 0` | 将 SDF 网格绑定到着色器变量 |
| `void bakePrimitives(uint32_t batchSize)` | 将基元烘焙到网格表示(sdfg 文件) |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | GPU 设备引用 |
| `mName` | `string` | 网格名称 |
| `mGridWidth` | `uint32_t` | 网格宽度(以体素为单位) |
| `mPrimitives` | `vector<SDF3DPrimitive>` | 基元列表 |
| `mPrimitiveIDToIndex` | `unordered_map<uint32_t, uint32_t>` | 基元 ID 到索引的映射 |
| `mPrimitivesDirty` | `bool` | 基元是否已变更 |
| `mpPrimitivesBuffer` | `ref<Buffer>` | GPU 上的基元结构化缓冲区 |
| `mpSDFGridTexture` | `ref<Texture>` | 保存值表示的 GPU 纹理 |
| `mpEvaluatePrimitivesPass` | `ref<ComputePass>` | 评估基元的计算通道 |

## 依赖关系

### 本文件引用
- `Core/Macros.h`, `Core/Object.h`, `Core/API/Buffer.h`, `Core/API/Texture.h`, `Core/Pass/ComputePass.h`
- `Scene/SDFs/SDF3DPrimitiveCommon.slang`
- `NormalizedDenseSDFGrid/NDSDFGrid.h`, `SparseVoxelSet/SDFSVS.h`, `SparseBrickSet/SDFSBS.h`, `SparseVoxelOctree/SDFSVO.h`
- `nlohmann/json.hpp` (JSON 序列化)
- `Utils/Logger.h`, `Utils/Math/Common.h`, `Utils/Scripting/ScriptBindings.h`

### 被以下文件引用
- 所有 SDF 网格子类 (`NDSDFGrid`, `SDFSVS`, `SDFSBS`, `SDFSVO`)
- `Scene` 类(友元类)

## 实现细节

- 构造函数要求设备支持 Shader Model 6.5
- `setPrimitives` 对非 SBS 类型要求 `gridWidth` 为 2 的幂
- 基元通过 ID-索引映射实现间接寻址,支持增删改查
- `createEvaluatePrimitivesPass` 创建计算通道,通过宏定义控制输出到 3D 纹理或缓冲区,以及是否与已有距离场合并
- `updatePrimitivesBuffer` 在基元数据变更时更新 GPU 结构化缓冲区
- 提供 Python 脚本绑定(通过 pybind11),支持从脚本创建各种 SDF 网格类型
- JSON 序列化支持前向兼容(可处理旧版的 uint 格式枚举值)
