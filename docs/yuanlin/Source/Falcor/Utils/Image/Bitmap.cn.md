# Bitmap 源码文档
> 路径: `Source/Falcor/Utils/Image/Bitmap.h` + `.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Image

## 功能概述
内存位图类，支持多种图像格式的加载、保存和格式转换，使用FreeImage和OpenEXR库处理不同文件格式。

## 类与接口

### `Bitmap`
- **继承**: 无
- **职责**: 管理内存中的图像数据，提供文件IO和格式转换功能

#### 枚举类型

##### `FileFormat`
| 枚举值 | 说明 |
|--------|------|
| `PngFile` | PNG格式，无损压缩8位图像，支持alpha |
| `JpegFile` | JPEG格式，有损压缩8位图像，不支持alpha |
| `TgaFile` | TGA格式，无损未压缩8位图像，支持alpha |
| `BmpFile` | BMP格式，无损未压缩8位图像，支持alpha |
| `PfmFile` | PFM格式，32位浮点HDR图像 |
| `ExrFile` | EXR格式，16/32位浮点HDR图像 |
| `DdsFile` | DDS格式，支持GPU资源格式和块压缩 |

##### `ExportFlags`
| 标志 | 说明 |
|------|------|
| `None` | 默认 |
| `ExportAlpha` | 保存alpha通道 |
| `Lossy` | 使用有损格式 |
| `Uncompressed` | 优先快速加载而非文件大小 |
| `ExrFloat16` | EXR使用半精度浮点 |

##### `ImportFlags`
| 标志 | 说明 |
|------|------|
| `None` | 默认 |
| `ConvertToFloat16` | 导入时将HDR图像转换为16位浮点 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `create(width, height, format, pData)` | 从内存创建位图 |
| `createFromFile(path, isTopDown, importFlags)` | 从文件加载位图 |
| `saveImage(path, width, height, fileFormat, exportFlags, format, isTopDown, pData)` | 保存图像到文件 |
| `getWidth()` | 获取宽度（像素） |
| `getHeight()` | 获取高度（像素） |
| `getData()` | 获取数据指针 |
| `getFormat()` | 获取资源格式 |
| `getRowPitch()` | 获取行间距（字节） |
| `getSize()` | 获取总大小（字节） |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpData` | `std::unique_ptr<uint8_t[]>` | 图像数据 |
| `mWidth` | `uint32_t` | 宽度（像素） |
| `mHeight` | `uint32_t` | 高度（像素） |
| `mRowPitch` | `uint32_t` | 行间距（字节） |
| `mSize` | `size_t` | 总大小（字节） |
| `mFormat` | `ResourceFormat` | 数据格式 |

## 依赖关系

### 本文件引用
- `Core/API/Formats.h`
- `Core/Platform/OS.h`
- FreeImage库
- OpenEXR库（ImfIO, ImfInputFile等）

### 被以下文件引用
- `AsyncTextureLoader.h`
- `ImageIO.h`
- `TextureManager.h`
- 纹理加载系统

## 实现细节

### 格式转换

#### 半精度浮点转换
```cpp
convertHalfToRGBA32Float(width, height, channelCount, pData)
```
- 将16位半精度浮点图像转换为32位RGBA浮点格式
- 缺失通道填充为0

#### 整数格式转换
```cpp
convertIntToRGBA32Float<SrcT>(width, height, channelCount, pData)
```
- 无符号整数归一化到[0,1]
- 有符号整数归一化到[-1,1]

### 文件格式支持

#### LDR格式（通过FreeImage）
- PNG: 支持压缩级别控制
- JPEG: 支持质量参数（0-100）
- TGA, BMP: 无损格式

#### HDR格式

##### OpenEXR
- 支持16位和32位浮点
- 使用内存映射文件优化大文件加载
- 自定义流接口（`OpenExrStream`）包装内存映射

##### PFM
- 32位浮点RGB格式
- 简单的文本头格式

### 内存布局
- 支持top-down和bottom-up两种布局
- `isTopDown=true`: 左上角像素在缓冲区开头
- `isTopDown=false`: 左下角像素在缓冲区开头

### 错误处理
- 文件不存在或格式错误时抛出异常
- 不支持的格式组合时记录警告
- 使用FALCOR_THROW宏统一异常处理
