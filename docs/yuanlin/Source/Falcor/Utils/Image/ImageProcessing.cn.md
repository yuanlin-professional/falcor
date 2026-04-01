# ImageProcessing 源码文档
> 路径: `Source/Falcor/Utils/Image/ImageProcessing.h` + `.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Image

## 功能概述
图像处理工具类，提供GPU加速的图像操作功能，当前实现了单通道颜色复制功能。

## 类与接口

### `ImageProcessing`
- **继承**: 无
- **职责**: 提供图像处理的GPU计算功能

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `ImageProcessing(ref<Device>)` | 构造函数，初始化设备引用 |
| `copyColorChannel(pRenderContext, pSrc, pDst, srcMask)` | 复制单个颜色通道到目标纹理 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | 设备对象 |
| `mpCopyFloatPass` | `ref<ComputePass>` | 浮点格式复制通道 |
| `mpCopyIntPass` | `ref<ComputePass>` | 整数格式复制通道 |

## 依赖关系

### 本文件引用
- `Core/API/RenderContext.h`
- `Core/Pass/ComputePass.h`
- `Utils/Image/CopyColorChannel.cs.slang`

### 被以下文件引用
- 图像编辑工具
- 纹理处理管线
- 后处理效果

## 实现细节

### copyColorChannel 功能

#### 参数验证
```cpp
// 检查资源类型
if (pSrc->getResource()->getType() != Resource::Type::Texture2D)
    FALCOR_THROW("Source resource type must be Texture2D");

// 检查维度匹配
if (any(srcDim != dstDim))
    FALCOR_THROW("Source and destination views must have matching dimensions");

// 检查格式类型匹配
if (srcIsInt != dstIsInt)
    FALCOR_THROW("Source and destination texture must have matching format type");
```

#### 通道映射
| srcMask | channelIndex |
|---------|--------------|
| `TextureChannelFlags::Red` | 0 |
| `TextureChannelFlags::Green` | 1 |
| `TextureChannelFlags::Blue` | 2 |
| `TextureChannelFlags::Alpha` | 3 |

#### 着色器选择
- 浮点格式：使用 `mpCopyFloatPass`，定义 `TEXTURE_FORMAT=float4`
- 整数格式：使用 `mpCopyIntPass`，定义 `TEXTURE_FORMAT=uint4`

#### 执行流程
1. 验证输入参数
2. 根据格式类型选择或创建计算通道
3. 绑定资源和参数
4. 调度计算着色器（16x16线程组）

### 着色器接口
```slang
Texture2D<TEXTURE_FORMAT> gSrc;
RWTexture2D<TEXTURE_FORMAT> gDst;

cbuffer CB {
    uint2 viewDim;
    uint channelIndex;
};
```

### 性能特性
- GPU并行处理，16x16线程组
- 支持任意2D纹理尺寸
- 延迟创建计算通道（按需初始化）
