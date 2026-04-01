# StringUtils 源码文档

> 路径: `Source/Falcor/Utils/StringUtils.h` + `Source/Falcor/Utils/StringUtils.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils

## 功能概述

提供一系列字符串操作工具函数，包括前缀/后缀检查、分割/连接、空白处理、字符替换、URI 解码、数组索引解析、字节大小格式化、大小写转换、编码转换以及 Base64 编解码。

## 类与接口

无类定义。所有功能以自由函数形式提供。

### 自由函数

| 函数签名 | 说明 |
|----------|------|
| `bool hasPrefix(const string& str, const string& prefix, bool caseSensitive)` | 检查字符串是否以指定前缀开头 |
| `bool hasSuffix(const string& str, const string& suffix, bool caseSensitive)` | 检查字符串是否以指定后缀结尾 |
| `vector<string> splitString(const string& str, const string& delim)` | 按分隔符分割字符串 |
| `string joinStrings(const vector<string>& strings, const string& separator)` | 用分隔符连接字符串数组 |
| `string removeLeadingWhitespace(const string& str, const char* whitespace)` | 去除前导空白 |
| `string removeTrailingWhitespace(const string& str, const char* whitespace)` | 去除尾部空白 |
| `string removeLeadingTrailingWhitespace(const string& str, const char* whitespace)` | 去除前后空白 |
| `string replaceCharacters(const string& str, const char* characters, char replacement)` | 替换指定字符集 |
| `string padStringToLength(const string& str, size_t length, char padding)` | 填充字符串到指定长度 |
| `string replaceSubstring(const string& input, const string& src, const string& dst)` | 替换所有子字符串 |
| `string decodeURI(const string& input)` | URI 解码 |
| `bool parseArrayIndex(const string& name, string& nonArray, uint32_t& index)` | 解析数组索引格式 `name[index]` |
| `void copyStringToBuffer(char* buffer, uint32_t bufferSize, const string& s)` | 安全拷贝字符串到缓冲区 |
| `string formatByteSize(size_t size)` | 将字节数格式化为可读字符串（B/kB/MB/GB/TB） |
| `string toLowerCase(const string& str)` | 转换为小写 |
| `wstring string_2_wstring(const string& s)` | ASCII 转 UTF-8 宽字符串 |
| `string wstring_2_string(const wstring& ws)` | UTF-8 宽字符串转 ASCII |
| `string utf32ToUtf8(uint32_t codepoint)` | UTF-32 码点转 UTF-8 |
| `string encodeBase64(const void* data, size_t len)` | Base64 编码 |
| `vector<uint8_t> decodeBase64(const string& in)` | Base64 解码 |

## 依赖关系

### 本文件引用

- `Core/Macros.h`、`Core/Error.h` — 宏和错误处理
- `<fmt/format.h>` — 格式化

### 被以下文件引用

- `PathResolving.cpp` — 字符串分割
- 几乎所有需要字符串处理的 Falcor 模块

## 实现细节

- `splitString` 逐字符遍历，遇到分隔符时切分
- `formatByteSize` 根据大小自动选择合适的单位
- Base64 编解码使用标准查找表实现
- `parseArrayIndex` 处理嵌套结构中的数组索引（如 `SomeStruct[1].v` 会被正确忽略）
