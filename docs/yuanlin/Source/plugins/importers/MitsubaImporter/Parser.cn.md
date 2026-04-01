# Parser 源码文档

> 路径: `Source/plugins/importers/MitsubaImporter/Parser.h`
> 类型: C++ 头文件（仅头文件，含内联实现）
> 模块: plugins/importers/MitsubaImporter

## 功能概述

Mitsuba XML 场景文件的完整解析器。定义了 Mitsuba 属性系统（`Properties`）、XML 标签/类枚举、版本处理、解析上下文以及递归 XML 遍历函数。负责将 Mitsuba XML 转换为对象实例映射。

## 类与接口

### `Properties`

- **职责**: 通用属性容器，支持 Bool、Int、Float、String、Float3、Color3、Transform 类型。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `getBool/getInt/getFloat/getString/getFloat3/getColor3/getTransform` | 获取各类型属性值（含默认值重载） |
| `setBool/setInt/setFloat/...` | 设置各类型属性值 |
| `hasBool/hasInt/hasFloat/...` | 检查属性是否存在且类型匹配 |
| `addNamedReference(name, id)` | 添加命名引用（子对象） |

### `Color3`

- **职责**: RGB 颜色值，支持到 `float3` 和 `float4` 的隐式转换。

### `Version`

- **职责**: 版本号解析和比较（major.minor.patch 格式）。

### `XMLObject`

- **职责**: 解析后的 XML 对象实例，包含 id、类型、属性和子引用。

### `XMLContext`

- **职责**: 解析上下文，维护实例映射、ID 计数器、当前变换和路径解析器。

### 枚举 `Tag` 和 `Class`

- `Tag`: XML 标签类型（Boolean、Integer、Float、String、Point、Vector、Transform、Object 等）
- `Class`: Mitsuba 插件类（Scene、Sensor、Emitter、Shape、BSDF、Texture、Medium 等）

### 关键函数

| 函数签名 | 说明 |
|----------|------|
| `parseXML(XMLSource&, XMLContext&, pugi::xml_node&, Tag, Properties&, size_t&, ...)` | 递归解析 XML 节点树，返回 (name, id) 对 |
| `upgradeTree(XMLSource&, pugi::xml_node&, const Version&)` | 将 v1 格式升级到 v2（驼峰转下划线、纹理参数转变换块等） |

## 依赖关系

### 本文件引用

- `Resolver.h` - 文件路径解析
- `pugixml.hpp` - XML DOM 解析
- Falcor 数学工具（向量、矩阵、数学辅助函数）

### 被以下文件引用

- `MitsubaImporter.cpp` - 调用 `parseXML` 解析场景

## 实现细节

- 支持所有 Mitsuba 变换操作：translate、rotate、scale、lookat、matrix（9 或 16 元素）。
- 属性类型使用 `std::variant` 实现类型安全的异构容器。
- v1→v2 升级自动将驼峰命名转为下划线命名，处理旧的 uoffset/voffset/uscale/vscale 参数。
- `include`、`alias`、`default`、`resource` 标签尚未实现。
