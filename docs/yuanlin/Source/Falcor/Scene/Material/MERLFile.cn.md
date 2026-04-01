# MERLFile 源码文档
> 路径: `Source/Falcor/Scene/Material/MERLFile.h` + `MERLFile.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Material

## 功能概述
实现了 MERL BRDF 数据库的文件加载功能。MERL 数据库包含基于实物测量的双向散射分布函数（BSDF）数据。该类负责读取二进制 BRDF 文件、将数据转换为 GPU 可用格式，并预计算反照率查找表 (Albedo LUT)。

## 类与接口

### `MERLFile`
- **继承**: 无
- **职责**: 加载和处理 MERL BRDF 测量数据

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `MERLFile(const path& path)` | 构造函数，加载指定路径的 MERL BRDF |
| `bool loadBRDF(const path& path)` | 加载 MERL BRDF 文件 |
| `const vector<float4>& prepareAlbedoLUT(ref<Device>)` | 准备反照率查找表，先尝试从磁盘加载，否则重新计算 |
| `const Desc& getDesc() const` | 获取 BRDF 描述信息 |
| `const vector<float3>& getData() const` | 获取 BRDF 数据 |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `mDesc` | `Desc` | BRDF 描述和采样参数 |
| `mData` | `vector<float3>` | BRDF 数据（RGB float 格式） |
| `mAlbedoLUT` | `vector<float4>` | 预计算的反照率查找表 |

## 依赖关系
### 本文件引用
- `DiffuseSpecularData.slang` - 漫反射/高光拟合参数
- `DiffuseSpecularUtils.h` - JSON 数据加载
- `Rendering/Materials/BSDFIntegrator.h` - 双向散射分布函数积分器

## 实现细节
- MERL BRDF 的角度采样分辨率：thetaH=90, thetaD=90, phiD=360
- 数据从 double 精度转换为 float，并应用 RGB 通道缩放因子
- 负值被钳制为零，inf/NaN 值被替换为零
- 反照率 LUT 缓存为 DDS 文件，避免重复计算
- 通过创建临时场景和 BSDFIntegrator 来计算各向同性反照率
