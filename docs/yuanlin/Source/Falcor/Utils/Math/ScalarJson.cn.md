# ScalarJson 源码文档

> 路径: `Source/Falcor/Utils/Math/ScalarJson.h`
> 类型: C++ 头文件
> 模块: Utils/Math

## 功能概述

提供 `float16_t` 类型与 JSON (nlohmann::json) 之间的序列化/反序列化支持。

## 类与接口

无类定义，提供内联函数：

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `void to_json(json& j, const float16_t& v)` | float16_t 序列化为 JSON（存为 double） |
| `void from_json(const json& j, float16_t& v)` | 从 JSON 反序列化 float16_t |

## 依赖关系
### 本文件引用
- `Float16.h`
- `Core/Error.h`
- `<nlohmann/json.hpp>`
