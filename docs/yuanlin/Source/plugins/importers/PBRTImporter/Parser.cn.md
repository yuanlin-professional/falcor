# Parser 源码文档

> 路径: `Source/plugins/importers/PBRTImporter/Parser.h`, `Parser.cpp`
> 类型: C++ 头文件 + 实现
> 模块: plugins/importers/PBRTImporter

## 功能概述

pbrt-v4 场景描述语言的词法分析器（`Tokenizer`）和语法解析器。将 `.pbrt` 文件转换为对 `ParserTarget` 接口的回调调用序列。基于 pbrt 项目的代码（Apache-2.0 许可）。

## 类与接口

### `ParserTarget`（抽象接口）

- **职责**: 定义 pbrt 命令回调接口，所有 pbrt 指令（变换、材质、形状、灯光等）均映射为此接口的虚函数调用。

#### 关键方法（共约 30 个虚函数）

| 方法签名 | 说明 |
|----------|------|
| `onIdentity/onTranslate/onRotate/onScale/onLookAt/onTransform/onConcatTransform` | 变换命令 |
| `onCamera/onFilm/onSampler/onIntegrator/onAccelerator/onPixelFilter` | 渲染设置 |
| `onWorldBegin` | 世界块开始 |
| `onAttributeBegin/onAttributeEnd/onAttribute` | 属性作用域 |
| `onShape/onMaterial/onMakeNamedMaterial/onNamedMaterial` | 形状和材质 |
| `onLightSource/onAreaLightSource` | 灯光 |
| `onTexture` | 纹理定义 |
| `onObjectBegin/onObjectEnd/onObjectInstance` | 实例 |
| `onEndOfFiles` | 文件结束 |

### `Tokenizer`

- **职责**: pbrt 文件的词法分析器，逐字符扫描生成 Token 序列。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static unique_ptr<Tokenizer> createFromFile(const path&)` | 从文件创建分词器 |
| `static unique_ptr<Tokenizer> createFromString(string)` | 从字符串创建分词器 |
| `optional<Token> next()` | 获取下一个 Token |

### `Token`

| 成员 | 类型 | 说明 |
|------|------|------|
| `token` | `string_view` | Token 文本（仅在下次 `next()` 前有效） |
| `loc` | `FileLoc` | 文件位置 |

## 全局函数

| 函数签名 | 说明 |
|----------|------|
| `void parseFile(ParserTarget&, const path&)` | 解析 pbrt 文件 |
| `void parseString(ParserTarget&, string)` | 解析 pbrt 字符串 |

## 依赖关系

### 本文件引用

- `Types.h` - `FileLoc`、`Float`
- `Parameters.h` - `ParsedParameterVector`

### 被以下文件引用

- `Builder.h` - `BasicSceneBuilder` 继承 `ParserTarget`
- `PBRTImporter.cpp` - 调用 `parseFile`

## 实现细节

- 词法分析器维护静态文件名列表，确保 `FileLoc::filename` 在分词器销毁后仍有效。
- 支持 UTF-16 检测。
- 支持 `Include` 指令的递归文件解析。
