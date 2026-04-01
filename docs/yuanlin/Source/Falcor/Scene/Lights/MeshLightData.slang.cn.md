# MeshLightData.slang 源码文档

> 路径: `Source/Falcor/Scene/Lights/MeshLightData.slang`
> 类型: Slang 着色器文件（主机/设备共享）
> 模块: Scene/Lights

## 功能概述

定义网格面积光源的数据结构 `MeshLightData`，描述一个自发光网格实例。包含实例 ID、三角形偏移、三角形数量和材质 ID。该结构在 CPU 和 GPU 之间共享。

## 结构体与接口

### `MeshLightData`

| 成员 | 类型 | 说明 |
|------|------|------|
| `instanceID` | `uint` | 场景中的几何实例 ID |
| `triangleOffset` | `uint` | 在 LightCollection 全局自发光三角形列表中的偏移 |
| `triangleCount` | `uint` | 网格光源中的三角形数量 |
| `materialID` | `uint` | 材质 ID（场景材质数组索引） |

| 常量 | 说明 |
|------|------|
| `kInvalidIndex = 0xFFFFFFFF` | 无效索引标记 |

## 依赖关系 / import

- `Utils/HostDeviceShared.slangh` — 主机/设备共享宏
