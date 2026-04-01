# Parameters 源码文档

> 路径: `Source/plugins/importers/PBRTImporter/Parameters.h`, `Parameters.cpp`
> 类型: C++ 头文件 + 实现
> 模块: plugins/importers/PBRTImporter

## 功能概述

pbrt 参数系统的实现。`ParsedParameter` 表示从 pbrt 文件解析的原始参数，`ParameterDictionary` 提供类型安全的参数查询接口，支持标量值和数组查询，以及光谱数据解析。

## 类与接口

### `ParsedParameter`

- **职责**: 存储从 pbrt 文件解析的单个参数（类型+名称+值列表）。

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `type` | `string` | 参数类型字符串 |
| `name` | `string` | 参数名称 |
| `loc` | `FileLoc` | 文件位置 |
| `floats/ints/strings/bools` | `vector<...>` | 各类型值列表 |
| `lookedUp` | `mutable bool` | 是否被查询过（用于未使用参数警告） |
| `colorSpace` | `const RGBColorSpace*` | 关联的颜色空间 |
| `mayBeUnused` | `bool` | 是否允许未被使用 |

### `ParameterDictionary`

- **职责**: 类型安全的参数查询接口。

#### 关键方法（标量查询）

| 方法签名 | 说明 |
|----------|------|
| `Float getFloat(name, def)` | 获取浮点参数 |
| `int getInt(name, def)` | 获取整数参数 |
| `string getString(name, def)` | 获取字符串参数 |
| `bool getBool(name, def)` | 获取布尔参数 |
| `float3 getPoint3/getVector3/getNormal(name, def)` | 获取三维向量参数 |
| `Spectrum getSpectrum(name, def, resolver)` | 获取光谱参数 |
| `string getTexture(name)` | 获取纹理引用名称 |

#### 关键方法（数组查询）

| 方法签名 | 说明 |
|----------|------|
| `vector<Float> getFloatArray(name)` | 获取浮点数组 |
| `vector<float3> getPoint3Array(name)` | 获取三维点数组 |
| `vector<float3> getNormalArray(name)` | 获取法线数组 |
| `vector<Spectrum> getSpectrumArray(name, resolver)` | 获取光谱数组 |

### `ParameterType` 枚举

- Float、Int、String、Bool、Point2、Vector2、Point3、Vector3、Normal、Spectrum、Texture

## 依赖关系

### 本文件引用

- `Types.h` - `FileLoc`、`Float`、`Spectrum`
- `Utils/Math/Vector.h` - 数学类型
- `Utils/Color/Spectrum.h` - 光谱类型

### 被以下文件引用

- `Builder.h` - `SceneEntity` 等使用 `ParameterDictionary`
- `Parser.h` - `ParsedParameterVector`
- `PBRTImporter.cpp` - 参数提取
