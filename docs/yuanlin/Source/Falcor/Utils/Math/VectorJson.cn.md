# VectorJson 源码文档

> 路径: `Source/Falcor/Utils/Math/VectorJson.h`
> 类型: C++ 头文件
> 模块: Utils/Math

## 功能概述

提供向量类型与 JSON (nlohmann::json) 之间的序列化/反序列化支持。向量数据以一维数组形式存储在 JSON 中。

## 类与接口

无类定义，提供模板函数：

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `void to_json(json& j, const vector<T,N>& v)` | 向量序列化为 JSON 数组 |
| `void from_json(const json& j, vector<T,N>& v)` | 从 JSON 数组反序列化向量 |

## 依赖关系
### 本文件引用
- `VectorTypes.h`
- `ScalarJson.h`
- `Core/Error.h`
- `<nlohmann/json.hpp>`
