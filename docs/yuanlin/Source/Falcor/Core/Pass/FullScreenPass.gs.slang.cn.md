# FullScreenPass.gs.slang 源码文档

> 路径: `Source/Falcor/Core/Pass/FullScreenPass.gs.slang`
> 类型: Slang 着色器（几何着色器）
> 模块: Core/Pass

## 功能概述

全屏渲染通道的多视口几何着色器。当需要将全屏渲染结果输出到多个渲染目标数组层（Render Target Array）时使用。通过遍历视口掩码中的每一位，将输入三角形复制并广播到对应的渲染目标层，实现多投影（multi-projection）渲染。

## 结构体与接口

### `VsOut`（输入结构体）

| 成员 | 类型 | 语义 | 说明 |
|------|------|------|------|
| `texC` | `float2` | `TEXCOORD` | 纹理坐标 |
| `posH` | `float4` | `POSITION` | 顶点位置（来自顶点着色器） |

### `GsOut`（输出结构体）

| 成员 | 类型 | 语义 | 说明 |
|------|------|------|------|
| `texC` | `float2` | `TEXCOORD` | 纹理坐标 |
| `posH` | `float4` | `SV_POSITION` | 系统值位置 |
| `rtIndex` | `uint` | `SV_RenderTargetArrayIndex` | 渲染目标数组层索引 |

## 函数

### `void main(triangle VsOut input[3], inout TriangleStream<GsOut> outStream)`

几何着色器入口函数。属性 `[maxvertexcount(_OUTPUT_VERTEX_COUNT)]` 声明最大输出顶点数。

**执行逻辑**:
1. 读取 `_VIEWPORT_MASK` 掩码
2. 使用 `firstbitlow` 找到掩码中最低有效位（对应渲染目标层索引）
3. 将输入三角形的 3 个顶点复制到输出流，设置 `rtIndex` 为当前层索引
4. 调用 `RestartStrip()` 结束当前三角形条带
5. 清除已处理的位，继续处理下一个层

## 依赖关系

### 预处理宏

| 宏名称 | 说明 |
|--------|------|
| `_VIEWPORT_MASK` | 视口掩码，每一位对应一个渲染目标层。由 `FullScreenPass.cpp` 定义 |
| `_OUTPUT_VERTEX_COUNT` | 最大输出顶点数，等于 `3 * popcount(_VIEWPORT_MASK)`。由 `FullScreenPass.cpp` 定义 |

### 被以下文件引用

- `FullScreenPass.cpp` — 当 `viewportMask` 非零时动态添加此几何着色器
