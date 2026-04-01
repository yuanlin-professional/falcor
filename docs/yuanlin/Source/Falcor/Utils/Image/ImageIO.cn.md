# ImageIO 源码文档
> 路径: `Source/Falcor/Utils/Image/ImageIO.h` + `.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Image

## 功能概述
DDS文件格式的专用IO类，支持块压缩格式、mipmap和纹理数组的加载与保存，使用NVTT库进行压缩。

## 类与接口

### `ImageIO`
- **继承**: 无
- **职责**: 处理DDS格式纹理的加载、保存和压缩

#### 枚举类型

##### `CompressionMode`
| 模式 | 说明 | 块大小 |
|------|------|--------|
| `BC1` | RGB + 1位alpha | 8字节/块 |
| `BC2` | RGBA，BC1(RGB) + 4位alpha | 16字节/块 |
| `BC3` | RGBA，BC1(RGB) + BC4(alpha) | 16字节/块 |
| `BC4` | 单通道灰度 | 8字节/块 |
| `BC5` | 双通道，每通道BC4 | 16字节/块 |
| `BC6` | RGB 16位浮点 | 16字节/块 |
| `BC7` | 8位RGB或RGBA | 16字节/块 |
| `None` | 无压缩 | - |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `loadBitmapFromDDS(path)` | 从DDS加载位图（仅第一张图像） |
| `loadTextureFromDDS(pDevice, path, loadAsSrgb)` | 从DDS加载纹理（完整数据） |
| `saveToDDS(path, bitmap, mode, generateMips)` | 保存位图到DDS |
| `saveToDDS(pContext, path, pTexture, mode, generateMips)` | 保存纹理到DDS |

## 依赖关系

### 本文件引用
- `Bitmap.h`
- `Core/API/Texture.h`
- `Core/API/CopyContext.h`
- NVTT库（nvtt/nvtt.h）
- DDSHeader库（dds_header/DDSHeader.h）

### 被以下文件引用
- `Bitmap.cpp`
- 纹理导出工具
- 资源管道

## 实现细节

### DDS文件结构
```
[DDS Magic] [DDS_HEADER] [DDS_HEADER_DXT10?] [Image Data]
```

#### 头部解析
- 标准DDS_HEADER：128字节
- 可选DX10扩展头：20字节
- 支持检测和处理两种头部格式

### 加载流程

#### 位图加载
1. 使用内存映射文件读取
2. 解析DDS头部
3. 验证格式和维度
4. 仅提取第一个mip级别的第一张图像
5. 创建Bitmap对象

#### 纹理加载
1. 解析完整的DDS结构
2. 读取所有mip级别和数组切片
3. 可选sRGB格式转换
4. 创建GPU纹理资源并上传数据

### 保存流程

#### 从位图保存
1. 配置NVTT压缩选项
2. 可选生成mipmap
3. 应用块压缩
4. 写入DDS文件

#### 从纹理保存
1. 使用CopyContext从GPU读回数据
2. 遍历所有子资源（mips + array slices）
3. 处理压缩和未压缩格式
4. 写入完整的DDS文件

### 压缩支持

#### NVTT集成
```cpp
nvtt::Context context;
nvtt::CompressionOptions compressionOptions;
nvtt::OutputOptions outputOptions;
```

#### 格式映射
- ResourceFormat → nvtt::Format
- 支持BC1-BC7所有块压缩格式
- 自动处理sRGB变体

### 特殊处理

#### 已压缩纹理
- 检测源格式是否已压缩
- 如果mode=None但格式已压缩，自动推断压缩模式
- NVTT的Surface仅支持未压缩数据，需重新压缩

#### Mipmap生成
- 使用NVTT的mipmap生成功能
- 支持各种过滤器（Box, Triangle, Kaiser等）
- 可配置mipmap链长度

### 错误处理
- 文件格式错误时抛出详细异常
- 不支持的格式组合时提前检测
- 使用FALCOR_THROW统一错误报告
