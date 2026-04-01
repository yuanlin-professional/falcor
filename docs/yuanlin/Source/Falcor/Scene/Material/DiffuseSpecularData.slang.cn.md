# DiffuseSpecularData.slang 着色器文档
> 路径: `Source/Falcor/Scene/Material/DiffuseSpecularData.slang`
> 类型: Slang 着色器（主机/设备共享）
> 模块: Scene/Material

## 功能概述
定义了漫反射/高光 BRDF 近似的参数结构 `DiffuseSpecularData`，用于存储对目标 BRDF 的最佳拟合参数。

## 结构体与接口

### `DiffuseSpecularData`
| 字段 | 类型 | 说明 |
|------|------|------|
| `baseColor` | `float3` | 线性空间基础颜色 |
| `roughness` | `float` | 感知线性粗糙度（高光瓣使用平方值） |
| `specular` | `float` | 高光参数 |
| `metallic` | `float` | 金属度 |
| `lossValue` | `float` | 拟合损失值（不用于渲染） |

## 依赖关系
### 被以下文件引用
- `MERLMaterialData.slang`, `MERLFile.h`, `DiffuseSpecularUtils.h`
