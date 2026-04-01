# ConvertedInput 源码文档

> 路径: `Source/Modules/USDUtils/ConvertedInput.h`, `Source/Modules/USDUtils/ConvertedInput.cpp`
> 类型: C++ 头文件 + 实现
> 模块: USDUtils

## 功能概述

将 USD 着色器输入（`UsdShadeInput`）转换为 Falcor 可用的材质输入表示。支持纹理路径、均匀值、纹理通道标记、sRGB 标记、纹理坐标变换等。处理 `UsdUVTexture`、`UsdPrimvarReader_float2`、`UsdTransform2d` 等标准 USD 着色器节点。

## 类与接口

### `ConvertedTexTransform`

- **职责**: 管理纹理坐标变换，确保同一材质中所有纹理使用一致的变换（Falcor 限制）。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void update(const ConvertedInput& input)` | 根据输入的纹理变换更新共享变换；若不同纹理指定了不同变换则发出警告 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `transform` | `Transform` | 纹理坐标的 2D 变换 |

### `ConvertedInput`

- **职责**: 表示一个已从 USD 着色图转换的材质输入，可以是均匀值或纹理引用。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ConvertedInput convertTexture(...)` | 将 `UsdUVTexture` 节点转换为 `ConvertedInput`，解析文件路径、通道、颜色空间、UDIM 等 |
| `static ConvertedInput convertFloat(...)` | 将浮点输入（可能是纹理或常量）转换为 `ConvertedInput` |
| `static ConvertedInput convertColor(...)` | 将颜色输入（可能是纹理或常量）转换为 `ConvertedInput` |
| `static bool convertTextureCoords(...)` | 解析 `UsdTransform2d` 节点，提取缩放/旋转/平移变换 |
| `bool isTextured() const` | 返回此输入是否引用了纹理 |
| `bool operator==(const ConvertedInput&) const` | 相等比较，用于材质去重 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `uniformValue` | `float4` | 均匀值，无纹理时使用 |
| `texturePath` | `std::string` | 纹理文件路径 |
| `texTransform` | `Transform` | 纹理坐标 2D 变换 |
| `textureScale` | `float4` | 纹理值缩放（仅用于自发光） |
| `channels` | `TextureChannelFlags` | 数据所在的纹理通道 |
| `loadSRGB` | `bool` | 是否以 sRGB 方式加载纹理 |
| `shadeInput` | `UsdShadeInput` | 关联的 USD 着色输入（用于警告信息） |

### `TextureEncoding` 枚举

| 值 | 说明 |
|----|------|
| `Unknown` | 未知编码 |
| `Linear` | 线性编码 |
| `Srgb` | sRGB 编码 |
| `Normal` | 法线贴图编码（期望 bias=(-1,-1), scale=(2,2)） |

## 依赖关系

### 本文件引用

- `Utils/Math/Vector.h` - 数学向量类型
- `Scene/Transform.h` - 变换类
- `USDUtils/USDHelpers.h` - USD 编译警告宏
- `USDUtils/USDUtils.h` - USD 工具函数（`getAttribute`、`getSourceInput`、`getLayerHandle`）
- `Core/API/Formats.h` - `TextureChannelFlags`
- `pxr/usd/usdShade/shader.h` - USD 着色器 API
- `pxr/usd/sdf/layerUtils.h` - 路径解析

### 被以下文件引用

- `PreviewSurfaceConverter/StandardMaterialSpec.h` - 标准材质规格
- `PreviewSurfaceConverter/PreviewSurfaceConverter.cpp` - 预览表面转换器

## 实现细节

- 纹理路径解析支持 UDIM 替换：遇到 `<UDIM>` 时替换为 `1001` 以加载纹理集中的第一张纹理。
- 颜色空间优先级：文件级 `colorSpace` 属性 > 连接级 `sourceColorSpace` 属性 > 调用方指定的默认编码。
- `UsdTransform2d` 变换按 SRT（缩放-旋转-平移）顺序组合。
- 提供了 `std::hash` 特化，使 `ConvertedInput` 和 `Transform` 可用于哈希容器，支持材质去重。
