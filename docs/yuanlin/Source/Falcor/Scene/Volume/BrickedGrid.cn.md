# BrickedGrid 源码文档

> 路径: `Source/Falcor/Scene/Volume/BrickedGrid.h`
> 类型: C++ 头文件
> 模块: Scene/Volume

## 功能概述

定义砖块化网格（Bricked Grid）数据结构，包含体素网格转换为 GPU 友好的砖块纹理图集所需的三个 3D 纹理。这种数据结构允许在 GPU 上高效地进行体积数据查找和层次化 majorant 查询。

## 类与接口

### `BrickedGrid`

- **继承**: 无
- **职责**: 存储砖块纹理图集的三个纹理引用。

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `range` | `ref<Texture>` | 范围纹理：存储每个砖块的 majorant（最大值）和 minorant（最小值），支持 mipmap 用于层次化 majorant 查询 |
| `indirection` | `ref<Texture>` | 间接寻址纹理：将砖块索引映射到图集中的位置 |
| `atlas` | `ref<Texture>` | 图集纹理：存储所有非空砖块的体素数据（BC4 压缩或 UNORM 格式） |

## 依赖关系

### 本文件引用

- `Core/API/Texture.h` — 纹理类

### 被以下文件引用

- `Grid.h` — Grid 类持有 BrickedGrid 成员
- `GridConverter.h` — 转换器生成 BrickedGrid
