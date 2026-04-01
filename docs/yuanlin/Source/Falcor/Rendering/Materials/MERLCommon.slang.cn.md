# MERLCommon.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/MERLCommon.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

提供 MERL BRDF 数据库的公共查询功能。MERL 数据以半向量/差向量坐标参数化存储，该文件实现了坐标转换和数据查找逻辑。

## 结构体与接口

### `MERLCommon`

- **职责**: 在局部坐标系中求值 MERL 实测 BRDF

#### 关键函数

| 函数签名 | 说明 |
|----------|------|
| `eval(wi, wo, brdfData, byteOffset)` | 求值 MERL BRDF，返回 f(wi,wo)*wo.z |
| `computeHalfDiffCoords(wi, wo)` | 计算半向量/差向量坐标 (thetaH, thetaD, phiD) |
| `rotateVector(v, axis, angle)` | 沿轴旋转向量 |
| `getThetaHIndex(thetaH)` | 非线性映射 thetaH 到索引 |
| `getThetaDIndex(thetaD)` | 线性映射 thetaD 到索引 |
| `getPhiDIndex(phiD)` | 线性映射 phiD 到索引 |

#### 采样分辨率常量

- thetaH: 90, thetaD: 90, phiD: 360

## 依赖关系

### include
- `Utils/Math/MathConstants.slangh`
