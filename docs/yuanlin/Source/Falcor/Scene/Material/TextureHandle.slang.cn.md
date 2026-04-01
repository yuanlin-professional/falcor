# TextureHandle.slang 着色器文档
> 路径: `Source/Falcor/Scene/Material/TextureHandle.slang`
> 类型: Slang 着色器（主机/设备共享）
> 模块: Scene/Material

## 功能概述
定义了 GPU 端的纹理句柄结构 `TextureHandle`，用于在着色器中引用纹理。支持 Uniform（常量值）和 Texture（传统纹理）两种模式，并支持 UDIM 纹理寻址。

## 结构体与接口

### `TextureHandle`
| 字段 | 位数 | 说明 |
|------|------|------|
| TextureID | 29 | 纹理 ID（Texture 模式下使用） |
| Mode | 2 | 句柄模式（Uniform 或 Texture） |
| UdimEnabled | 1 | UDIM 启用标志 |

#### 关键方法
| 方法 | 说明 |
|------|------|
| `setMode/getMode` | 设置/获取模式 |
| `setTextureID/getTextureID` | 设置/获取纹理 ID |
| `setUdimEnabled/getUdimEnabled` | 设置/获取 UDIM 标志 |

### `TextureInfo`
纹理信息结构：width, height, depth, mipLevels。

## 依赖关系
### import
- `Utils/HostDeviceShared.slangh`
