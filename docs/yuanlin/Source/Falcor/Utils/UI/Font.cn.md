# Font 源码文档

> 路径: `Source/Falcor/Utils/UI/Font.h` + `Source/Falcor/Utils/UI/Font.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/UI

## 功能概述

Font 类用于加载和管理等宽字体资源，为文本渲染（TextRenderer）提供字体纹理和字符布局信息。它从自定义二进制格式（`.bin` + `.dds`）中读取字体数据，包括每个字符在纹理图集中的位置和尺寸信息。

## 类与接口

### `Font`

- **继承**: 无
- **职责**: 加载等宽字体的纹理图集和字符描述数据，提供字符查询接口供文本渲染使用

#### 内部结构体

| 结构体 | 说明 |
|--------|------|
| `CharTexCrdDesc` | 描述单个字符在纹理图集中的位置，包含 `topLeft`（非归一化原点）和 `size`（像素尺寸） |
| `FontFileHeader`（cpp 内部） | 字体二进制文件头，含魔数校验、字符数量、字体高度、制表符宽度、字符间距 |
| `FontCharData`（cpp 内部） | 单个字符的二进制数据，含字符值、左上角坐标、宽高 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `Font(ref<Device> pDevice, const std::filesystem::path& path)` | 构造函数，从指定路径加载字体资源，失败时抛出异常 |
| `ref<Texture> getTexture() const` | 获取字体纹理图集 |
| `const CharTexCrdDesc& getCharDesc(char c) const` | 获取指定 ASCII 字符的纹理坐标描述（范围 `!` 到 `~`） |
| `float getFontHeight() const` | 获取字体高度（像素） |
| `float getTabWidth() const` | 获取制表符宽度（像素） |
| `float getLettersSpacing() const` | 获取字符间距（像素），即相邻字符起始位置之差 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpTexture` | `ref<Texture>` | 字体纹理图集 |
| `mCharDesc` | `CharTexCrdDesc[mCharCount]` | 所有可打印字符的纹理坐标描述数组 |
| `mFontHeight` | `float` | 字体高度 |
| `mTabWidth` | `float` | 制表符宽度 |
| `mLetterSpacing` | `float` | 字符间距，取所有字符最大宽度 |
| `mFirstChar` | `static const char` | 首个可打印字符 `'!'` |
| `mLastChar` | `static const char` | 末尾可打印字符 `'~'` |
| `mTexWidth` | `static const uint32_t` | 纹理宽度固定为 1024 |

## 依赖关系

### 本文件引用

- `Core/Error.h` — 错误处理与断言
- `Core/API/fwd.h` — 前向声明
- `Core/API/Texture.h` — 纹理资源（`Texture::createFromFile` 加载 DDS）
- `Utils/Math/Vector.h` — `float2` 向量类型

### 被以下文件引用

- `Utils/UI/TextRenderer.cpp` — 使用 Font 获取字体纹理和字符描述进行文本渲染

## 实现细节

- 字体数据采用自定义二进制格式，通过魔数 `0xDEAD0001` 进行校验
- 文件加载时对头部结构大小、魔数、字符数据大小、字符数量进行严格校验
- 纹理使用 DDS 格式，通过 `Texture::createFromFile` 加载
- 字符间距取所有字符宽度的最大值，确保等宽排列
- 支持的字符范围为 ASCII `!`(0x21) 到 `~`(0x7E)，共 94 个字符
- 拷贝构造和赋值运算符被显式删除，防止资源的意外复制
