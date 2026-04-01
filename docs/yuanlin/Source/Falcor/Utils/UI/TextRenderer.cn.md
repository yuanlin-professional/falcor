# TextRenderer 源码文档

> 路径: `Source/Falcor/Utils/UI/TextRenderer.h` + `Source/Falcor/Utils/UI/TextRenderer.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/UI

## 功能概述

TextRenderer 类负责将文本字符串渲染到屏幕上。它使用 Font 类提供的字体纹理图集，通过光栅化管线将每个字符转换为两个三角形（一个四边形），并使用 `TextRenderer.3d.slang` 着色器进行渲染。支持阴影效果（通过偏移一个像素再次渲染黑色文本实现）。

## 类与接口

### `TextRenderer`

- **继承**: 无
- **职责**: 将 ASCII 文本渲染到指定的 FBO 上

#### 枚举

| 枚举 | 说明 |
|------|------|
| `Flags::None` | 无特殊效果 |
| `Flags::Shadowed` | 启用文本阴影（默认启用） |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `TextRenderer(ref<Device> pDevice)` | 构造函数：创建 VAO、RasterPass、加载字体和着色器 |
| `void render(RenderContext* pRenderContext, const std::string& text, const ref<Fbo>& pDstFbo, float2 pos)` | 渲染文本到目标 FBO，支持阴影效果 |
| `const float3& getColor() const` | 获取当前文本颜色 |
| `void setColor(const float3& color)` | 设置文本颜色 |
| `Flags getFlags() const` | 获取渲染标志 |
| `void setFlags(Flags flags)` | 设置渲染标志 |

#### 私有方法

| 方法签名 | 说明 |
|----------|------|
| `void setCbData(const ref<Fbo>& pDstFbo)` | 设置常量缓冲区数据（视口变换矩阵和字体颜色） |
| `void renderText(RenderContext*, const std::string&, const ref<Fbo>&, float2 pos)` | 内部文本渲染实现：构建顶点缓冲并提交绘制 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | GPU 设备引用 |
| `mFlags` | `Flags` | 渲染标志，默认 `Shadowed` |
| `mColor` | `float3` | 文本颜色，默认白色 |
| `mpVaos` | `ref<Vao>[kVaoCount]` | 3 个轮转 VAO，避免 GPU 停顿 |
| `mVaoIndex` | `uint32_t` | 当前 VAO 索引 |
| `mpPass` | `ref<RasterPass>` | 光栅化渲染 Pass |
| `mpFont` | `std::unique_ptr<Font>` | 字体资源 |

### 内部类型（cpp 匿名命名空间）

| 类型/常量 | 说明 |
|-----------|------|
| `Vertex` | 顶点结构体，含 `screenPos`（屏幕位置）和 `texCoord`（纹理坐标） |
| `kVertexPos[6]` | 四边形的 6 个顶点位置（两个三角形） |
| `kMaxCharCount` | 单次渲染最大字符数，1000 |

## 依赖关系

### 本文件引用

- `Core/Macros.h` — Falcor 宏
- `Core/API/fwd.h` — 前向声明
- `Core/API/Buffer.h` — GPU 缓冲区
- `Core/API/FBO.h` — 帧缓冲对象
- `Core/API/RenderContext.h` — 渲染上下文
- `Core/Pass/RasterPass.h` — 光栅化 Pass
- `Utils/Math/Vector.h` — 数学向量类型
- `Utils/UI/Font.h` — 字体资源
- `Utils/UI/TextRenderer.3d.slang` — 文本渲染着色器

### 被以下文件引用

- Falcor 框架的调试信息显示、HUD 文本叠加等功能

## 实现细节

- 每个字符渲染为一个四边形（2 个三角形，6 个顶点），顶点数据动态生成
- 使用 3 个轮转 VAO（triple buffering）避免 GPU 停顿：每帧使用不同的 VAO，下一帧轮转
- 顶点缓冲使用 `MemoryType::Upload` 类型，支持 CPU 端直接 map 写入
- 视口变换矩阵在 `setCbData` 中构建：将像素坐标转换为 NDC（[-1,1]），支持 `FALCOR_FLIP_Y` 宏控制 Y 轴方向
- 阴影效果通过两次渲染实现：先以黑色在 (pos + 1, pos + 1) 位置渲染，再以正常颜色在原位置渲染
- 支持特殊字符处理：`\n`（换行）、`\t`（制表符）、空格各自按字体参数调整位置
- 深度测试禁用，使用 Alpha 混合（SrcAlpha / OneMinusSrcAlpha），背面剔除关闭
- 默认字体为 `dejavu-sans-mono-14`，从运行时目录加载
- 单次渲染最多支持 1000 个字符
