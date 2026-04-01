# SceneDefines.slangh 源码文档

> 路径: `Source/Falcor/Scene/SceneDefines.slangh`
> 类型: Slang 头文件（主机/设备共享）
> 模块: Scene

## 功能概述

SceneDefines.slangh 定义了场景几何类型的宏常量和条件编译辅助宏。这些定义在 CPU 和 GPU 之间共享，用于在着色器编译期根据场景中实际存在的几何类型进行条件编译，避免不必要的代码路径。

## 结构体与接口

无结构体。仅包含宏定义。

## 宏定义

### 几何类型常量

| 宏名称 | 值 | 说明 |
|--------|---|------|
| `GEOMETRY_TYPE_NONE` | 0 | 无几何体 |
| `GEOMETRY_TYPE_TRIANGLE_MESH` | 1 | 三角形网格 |
| `GEOMETRY_TYPE_DISPLACED_TRIANGLE_MESH` | 2 | 位移三角形网格 |
| `GEOMETRY_TYPE_CURVE` | 3 | 曲线 |
| `GEOMETRY_TYPE_SDF_GRID` | 5 | SDF 网格 |
| `GEOMETRY_TYPE_CUSTOM` | 6 | 自定义图元 |

### 条件编译辅助宏

| 宏名称 | 说明 |
|--------|------|
| `SCENE_HAS_GEOMETRY_TYPE(_type_)` | 检查场景是否包含指定的几何类型（基于 `SCENE_GEOMETRY_TYPES` 位掩码） |
| `SCENE_HAS_PROCEDURAL_GEOMETRY()` | 检查场景是否包含程序化几何体（非三角形网格的任何类型） |

## 依赖关系 / import

- `Utils/HostDeviceShared.slangh` - 主机/设备共享宏
