# GBufferRT 源码文档

> 路径: `Source/RenderPasses/GBuffer/GBuffer/GBufferRT.h` + `Source/RenderPasses/GBuffer/GBuffer/GBufferRT.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/GBuffer

## 功能概述

基于光线追踪的 G-buffer 生成渲染通道。支持传统光线追踪（TraceRay）和内联光线追踪（TraceRayInline）两种模式，提供多种纹理 LOD 计算方法（Mip0、光线锥、光线微分），支持景深（DOF）计算，并输出丰富的 G-buffer 数据包括可见性缓冲区、深度、运动矢量、法线粗糙度等。

## 类与接口

### `GBufferRT`

- **继承**: `GBuffer`
- **职责**: 使用光线追踪生成 G-buffer 数据。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `reflect(const CompileData&)` | 声明输出端口（标准 G-buffer + 额外通道），均为 UAV |
| `execute(RenderContext*, const RenderData&)` | 根据设置选择 `executeRaytrace` 或 `executeCompute` |
| `executeRaytrace(...)` | 使用 TraceRay API 执行光线追踪 |
| `executeCompute(...)` | 使用 TraceRayInline（计算着色器）执行光线追踪 |
| `getShaderDefines(...)` | 获取着色器宏定义（DOF、Alpha 测试、LOD 模式等） |
| `bindShaderData(...)` | 绑定帧参数和输出纹理 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mLODMode` | `TexLODMode` | 纹理 LOD 计算模式 |
| `mUseTraceRayInline` | `bool` | 是否使用内联光线追踪 |
| `mUseDOF` | `bool` | 是否启用景深 |
| `mRaytrace` | 结构体 | 光线追踪程序和变量 |
| `mpComputePass` | `ref<ComputePass>` | 计算通道（内联模式） |

### 额外输出通道

| 通道名 | 格式 | 说明 |
|--------|------|------|
| `vbuffer` | 运行时决定 | 可见性缓冲区 |
| `depth` | R32Float | NDC 深度 |
| `linearZ` | RG32Float | 线性 Z 和斜率 |
| `normWRoughnessMaterialID` | RGB10A2Unorm | 引导法线、粗糙度和材质 ID |
| `diffuseOpacity` | RGBA32Float | 漫反射反照率和不透明度 |
| `specRough` | RGBA32Float | 镜面反射率和粗糙度 |
| `emissive` | RGBA32Float | 发射颜色 |
| `viewW` | RGBA32Float | 视线方向 |
| `disocclusion` | R32Float | 遮挡掩码 |

## 依赖关系

### 本文件引用

- `GBuffer.h` — 基类
- `Utils/Sampling/SampleGenerator.h` — 样本生成器
- `GBufferRT.rt.slang`, `GBufferRT.cs.slang` — 着色器文件

### 被以下文件引用

- `GBufferBase.cpp` — 插件注册

## 实现细节

- 需要 Shader Model 6.5 和 Raytracing Tier 1.1 支持
- 光线追踪模式下使用 SBT（Shader Binding Table）配置不同几何类型的命中组
- 支持四种几何类型的交叉测试：三角网格、位移三角网格、曲线、SDF 网格
