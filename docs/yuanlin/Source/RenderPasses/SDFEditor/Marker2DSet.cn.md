# Marker2DSet 源码文档

> 路径: `Source/RenderPasses/SDFEditor/Marker2DSet.h` + `Marker2DSet.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/SDFEditor

## 功能概述

2D 标记集合管理类，用于 SDF 编辑器的 2D GUI 覆盖层渲染。管理一组 2D SDF 标记（圆形、方形、圆角线、三角形、圆角矩形、箭头、向量、圆弧扇区、操作标记等），在 CPU 端构建标记数据并上传到 GPU 缓冲区供着色器渲染。

## 类与接口

### `Marker2DSet`

- **继承**: 无
- **命名空间**: `Falcor`
- **职责**: 管理 2D 标记的集合，提供添加各种形状的接口，并将数据绑定到着色器

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `Marker2DSet(ref<Device>, uint32_t maxMarkerCount)` | 构造函数，指定最大标记数 |
| `void clear()` | 清除所有标记 |
| `void addSimpleMarker(SDF2DShapeType, float, float2&, float, float4&)` | 添加简单形状标记 |
| `void addRoundedLine(float2&, float2&, float, float4&)` | 添加圆角线 |
| `void addTriangle(float2&, float2&, float2&, float4&)` | 添加三角形 |
| `void addRoundedBox(float2&, float2&, float, float, float4&)` | 添加圆角矩形 |
| `void addMarkerOpMarker(SDFOperationType, ...)` | 添加两个标记及其操作的复合标记 |
| `void addArrowFromTwoTris(float2&, float2&, float, float, float, float4&)` | 添加由两个三角形构成的箭头 |
| `void addVector(float2&, float2&, float, float, float4&)` | 添加向量（带箭头的线） |
| `void addCircleSector(float2&, float, float, float, float, float4&, float4&, ExcludeBorderFlags)` | 添加圆弧扇区 |
| `void bindShaderData(const ShaderVar&)` | 绑定标记数据到着色器变量 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | 设备引用 |
| `mMaxMarkerCount` | `uint32_t` | 最大标记数量 |
| `mMarkers` | `vector<Marker2DDataBlob>` | 标记数据列表 |
| `mpMarkerBuffer` | `ref<Buffer>` | GPU 结构化缓冲区 |
| `mDirtyBuffer` | `bool` | 缓冲区是否需要更新 |

## 依赖关系

### 本文件引用

- `Falcor.h`
- `Marker2DTypes.slang` — 2D 标记类型定义

### 被以下文件引用

- `SDFEditor.h` — SDF 编辑器使用 Marker2DSet 管理 GUI 标记
- `SelectionWheel.h` — 选择轮使用 Marker2DSet 渲染扇区

## 实现细节

- 标记数据以 `Marker2DDataBlob` 形式存储，包含类型标识和固定大小的 payload（20 个 uint）
- 不同形状的数据通过 `reinterpret_cast` 写入 payload
- GPU 缓冲区采用延迟更新策略：标记变化时设置脏标记，在 `bindShaderData` 时才更新
- 缓冲区大小不足时重新创建，否则就地更新
