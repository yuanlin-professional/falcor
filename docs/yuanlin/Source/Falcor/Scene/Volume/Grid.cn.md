# Grid 源码文档

> 路径: `Source/Falcor/Scene/Volume/Grid.h`, `Source/Falcor/Scene/Volume/Grid.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Volume

## 功能概述

基于 NanoVDB 的体素网格类，用于存储和访问体积数据（如密度场、发射场）。支持从 OpenVDB (.vdb) 和 NanoVDB (.nvdb) 文件加载，也支持程序化创建球体和盒子形状的雾体积。网格数据同时保存在主机端（NanoVDB）和设备端（GPU 缓冲区 + 砖块纹理图集）。

## 类与接口

### `Grid`

- **继承**: `Object`
- **职责**: 管理 NanoVDB 体素网格的主机和设备端数据，提供数据查询和着色器绑定。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<Grid> createSphere(ref<Device>, float radius, float voxelSize, float blendRange)` | 创建球形雾体积网格 |
| `static ref<Grid> createBox(ref<Device>, float width, float height, float depth, float voxelSize, float blendRange)` | 创建盒形雾体积网格 |
| `static ref<Grid> createFromFile(ref<Device>, const path&, const string& gridname)` | 从文件加载网格 |
| `void renderUI(Gui::Widgets& widget)` | 渲染 UI（体素数、索引范围、值范围、内存） |
| `void bindShaderData(const ShaderVar& var)` | 绑定 NanoVDB 缓冲区和砖块纹理到着色器 |
| `int3 getMinIndex() const` | 获取最小索引（对齐到砖块边界，8 的倍数） |
| `int3 getMaxIndex() const` | 获取最大索引（对齐到砖块边界） |
| `float getMinValue() const` | 获取存储的最小值 |
| `float getMaxValue() const` | 获取存储的最大值 |
| `uint64_t getVoxelCount() const` | 获取活跃体素数 |
| `uint64_t getGridSizeInBytes() const` | 获取 GPU 总内存（NanoVDB + 砖块纹理） |
| `AABB getWorldBounds() const` | 获取世界空间包围盒 |
| `float getValue(const int3& ijk) const` | 通过索引查询值（CPU端，非线程安全） |
| `float4x4 getTransform() const` | 获取 NanoVDB 仿射变换矩阵 |
| `float4x4 getInvTransform() const` | 获取逆仿射变换矩阵 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mGridHandle` | `nanovdb::GridHandle<HostBuffer>` | NanoVDB 网格句柄 |
| `mpFloatGrid` | `nanovdb::FloatGrid*` | 浮点网格指针 |
| `mAccessor` | `FloatGrid::AccessorType` | 网格访问器（CPU端） |
| `mpBuffer` | `ref<Buffer>` | GPU 端 NanoVDB 数据缓冲区 |
| `mBrickedGrid` | `BrickedGrid` | 砖块纹理图集数据 |

## 依赖关系

### 本文件引用

- `BrickedGrid.h` — 砖块网格纹理结构
- `GridConverter.h` — NanoVDB 到砖块纹理转换器
- `nanovdb/NanoVDB.h`, `nanovdb/util/IO.h` — NanoVDB 库
- `openvdb/openvdb.h` — OpenVDB 库
- `Utils/Scripting/ScriptBindings.h` — Python 绑定

### 被以下文件引用

- `GridVolume.h` — 体积对象使用网格
- `Scene/Scene.h` — 场景管理
- 着色器端的 `Grid.slang`

## 实现细节

- 索引包围盒对齐到 8 的倍数（砖块边界），以适配砖块纹理路径。
- GPU 数据包含 NanoVDB 原始缓冲区和 BC4 压缩的砖块纹理图集，两者同时驻留在 GPU 内存中。
- 从 OpenVDB 加载时，先使用 `nanovdb::openToNanoVDB` 转换为 NanoVDB 格式。
- 构造时如果网格缺少 min/max 统计信息，则调用 `nanovdb::gridStats` 计算。
- 使用 `NanoVDBConverterBC4` 将 NanoVDB 转换为砖块纹理图集。
