# SDF3DPrimitive.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SDF3DPrimitive.slang`
> 类型: Slang 着色器文件
> 模块: Scene/SDFs

## 功能概述

为 `SDF3DPrimitive` 结构体提供 GPU 端的扩展方法(extension),实现 SDF 基元的形状评估、区间评估以及 CSG 操作(并集、差集、交集及其平滑变体)。这些方法用于在 GPU 上评估基元对空间中任意点的有符号距离值。

## 结构体与接口

### `SDF3DPrimitive` (extension)

#### 函数

| 方法签名 | 说明 |
|----------|------|
| `float eval(float3 p)` | 评估基元在点 p 处的有符号距离值 |
| `float eval(float3 p, float d)` | 评估基元在点 p 处的距离值,并与已有距离 d 执行 CSG 操作 |
| `float2 evalInterval(float3 pCenter, float3 pHalfExtent, float2 d)` | 使用区间算术评估基元在包围盒内的距离范围 |
| `static float evalShape(float3 p, SDF3DShapeType, float3, float, float3, float3x3)` | 评估指定形状在点 p 的距离值(支持球体、椭球体、盒子、环面、锥体、胶囊体) |
| `static float2 evalIntervalShape(float3, float3, SDF3DShapeType, ...)` | 使用区间算术评估形状距离范围 |
| `static float evalOperation(SDFOperationType, float d, float dShape, float smoothing)` | 执行 CSG 操作(并集/差集/交集及其平滑变体) |
| `static float2 evalIntervalOperation(SDFOperationType, float2 d, float2 dShape, float smoothing)` | 使用区间算术执行 CSG 操作 |

## 依赖关系

### import
- `Scene.SDFs.SDF3DPrimitiveCommon` (导出)
- `Utils.SDF.SDF3DShapes` (SDF 形状函数)
- `Utils.SDF.SDFOperations` (CSG 操作函数)
- `Utils.Math.IntervalArithmetic` (区间算术)

### include
- `Utils/HostDeviceShared.slangh`, `Utils/Math/MathConstants.slangh`

## 实现细节

- 形状评估先将点通过逆旋转缩放矩阵变换到基元局部空间,然后调用对应形状的 SDF 函数
- 代码中显式使用 `transpose(invRotationScale)` 进行矩阵变换,这是为了保持与历史代码的兼容性(注释中标注这可能是一个 bug)
- 膨胀(blobbing)通过从距离值中减去膨胀值来实现
- 区间评估使用 `ivlSub` 等区间算术函数处理距离范围
