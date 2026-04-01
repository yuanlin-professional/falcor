# Resolver 源码文档

> 路径: `Source/plugins/importers/MitsubaImporter/Resolver.h`
> 类型: C++ 头文件（仅头文件）
> 模块: plugins/importers/MitsubaImporter

## 功能概述

简单的文件路径解析器，维护搜索路径列表。给定相对路径时，按列表顺序尝试各搜索路径，返回第一个存在的组合路径。

## 类与接口

### `Resolver`

- **职责**: 管理搜索路径列表，将相对文件路径解析为绝对路径。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void prepend(const path&)` | 在搜索列表头部添加路径 |
| `void append(const path&)` | 在搜索列表尾部添加路径 |
| `path resolve(const path&) const` | 在搜索路径中查找文件，返回第一个存在的完整路径；未找到时返回原路径 |
| `size_t size() const` | 搜索路径数量 |
| `begin()` / `end()` | 迭代器支持 |

## 依赖关系

### 被以下文件引用

- `Parser.h` - 作为 `XMLContext` 的成员，用于解析资源文件路径
