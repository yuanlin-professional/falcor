# Tables 源码文档

> 路径: `Source/plugins/importers/MitsubaImporter/Tables.h`
> 类型: C++ 头文件（仅头文件，含实现）
> 模块: plugins/importers/MitsubaImporter

## 功能概述

提供常见材料的折射率（IOR）查找表和查找函数。支持通过材料名称字符串获取对应的折射率值。

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float lookupIOR(std::string name)` | 按名称查找折射率。名称不区分大小写。未找到时输出警告并返回 0 |

## IOR 数据表

| 材料 | IOR |
|------|-----|
| vacuum | 1.0 |
| helium | 1.000036 |
| air | 1.000277 |
| water | 1.333 |
| ethanol | 1.361 |
| glycerol | 1.4729 |
| silicone oil | 1.52045 |
| fused quartz | 1.458 |
| acrylic glass | 1.49 |
| polypropylene | 1.49 |
| bk7 | 1.5046 |
| amber | 1.55 |
| diamond | 2.419 |

（完整列表见源码中的 `kIORTable`）

## 依赖关系

### 被以下文件引用

- `MitsubaImporter.cpp` - BSDF 构建时查找 IOR
