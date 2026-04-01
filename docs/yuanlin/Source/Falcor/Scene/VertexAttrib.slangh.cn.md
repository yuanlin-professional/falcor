# VertexAttrib.slangh 源码文档

> 路径: `Source/Falcor/Scene/VertexAttrib.slangh`
> 类型: Slang 头文件（主机/设备共享）
> 模块: Scene

## 功能概述

VertexAttrib.slangh 定义了顶点属性的布局位置（Location）和语义名称常量，在 CPU 端的 VAO 设置和 GPU 端的顶点着色器输入之间保持一致。同时定义了曲线顶点的属性布局。

## 结构体与接口

无结构体。仅包含宏定义常量。

## 宏定义

### 网格顶点属性

| 宏名称 | 值 | 说明 |
|--------|---|------|
| `VERTEX_POSITION_LOC` | 0 | 顶点位置的布局位置 |
| `VERTEX_PACKED_NORMAL_TANGENT_CURVE_RADIUS_LOC` | 1 | 打包法线/切线/曲线半径的布局位置 |
| `VERTEX_TEXCOORD_LOC` | 2 | 纹理坐标的布局位置 |
| `INSTANCE_DRAW_ID_LOC` | 3 | 实例 Draw ID 的布局位置 |
| `VERTEX_LOCATION_COUNT` | 4 | 顶点属性位置总数 |
| `VERTEX_POSITION_NAME` | `"POSITION"` | 位置语义名称 |
| `VERTEX_PACKED_NORMAL_TANGENT_CURVE_RADIUS_NAME` | `"PACKED_NORMAL_TANGENT_CURVE_RADIUS"` | 打包属性语义名称 |
| `VERTEX_TEXCOORD_NAME` | `"TEXCOORD"` | 纹理坐标语义名称 |
| `INSTANCE_DRAW_ID_NAME` | `"DRAW_ID"` | Draw ID 语义名称 |

### 曲线顶点属性

| 宏名称 | 值 | 说明 |
|--------|---|------|
| `CURVE_VERTEX_POSITION_LOC` | 0 | 曲线顶点位置 |
| `CURVE_VERTEX_RADIUS_LOC` | 1 | 曲线顶点半径 |
| `CURVE_VERTEX_TEXCOORD_LOC` | 2 | 曲线纹理坐标 |
| `CURVE_VERTEX_LOCATION_COUNT` | 3 | 曲线顶点属性位置总数 |

## 依赖关系 / import

- `Utils/HostDeviceShared.slangh` - 主机/设备共享宏
