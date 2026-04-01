# AlphaTest.slang 着色器文档
> 路径: `Source/Falcor/Scene/Material/AlphaTest.slang`
> 类型: Slang 着色器
> 模块: Scene/Material

## 功能概述
实现了 Alpha 测试的多种模式，包括基本阈值测试、基于哈希的各向同性和各向异性 Alpha 测试。哈希 Alpha 测试通过在世界空间中生成伪随机阈值来减少 Alpha 测试中的空间走样。

## 结构体与接口

### `AlphaTestMode` 枚举
| 值 | 说明 |
|----|------|
| `Disabled` | 禁用 Alpha 测试 |
| `Basic` | 基本阈值测试 |
| `HashedIsotropic` | 各向同性哈希 Alpha 测试 |
| `HashedAnisotropic` | 各向异性哈希 Alpha 测试 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `bool evalAlphaTest(float alpha, float threshold, float3 posW)` | 评估 Alpha 测试，根据当前模式选择测试方法 |
| `bool evalBasicAlphaTest(float alpha, float threshold)` | 基本硬阈值 Alpha 测试 |
| `bool evalHashedAlphaTest(float alpha, float materialThreshold, float hashedThreshold)` | 哈希 Alpha 测试 |
| `float calculateHashedAlpha(float3 hashInputCoord, float hashScale, bool useAnisotropy)` | 计算哈希 Alpha 阈值 |
| `float sineHash(float2 p)` | 2D 正弦哈希函数 |
| `float sineHash3D(float3 p)` | 3D 正弦哈希函数 |

## 依赖关系
### import
- 无外部 import

### 被以下文件引用
- `MaterialSystem.slang` - Alpha 测试入口
