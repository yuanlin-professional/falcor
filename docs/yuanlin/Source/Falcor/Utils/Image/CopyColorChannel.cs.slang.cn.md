# CopyColorChannel.cs.slang 源码文档
> 路径: `Source/Falcor/Utils/Image/CopyColorChannel.cs.slang`
> 类型: Slang 计算着色器
> 模块: Utils/Image

## 功能概述
GPU计算着色器，用于将源纹理的单个颜色通道复制到目标纹理的所有通道。

## 资源绑定

### 纹理资源
```slang
Texture2D<TEXTURE_FORMAT> gSrc;        // 源纹理（只读）
RWTexture2D<TEXTURE_FORMAT> gDst;      // 目标纹理（读写）
```

### 常量缓冲区
```slang
cbuffer CB {
    uint2 viewDim;        // 视图维度（宽度，高度）
    uint channelIndex;    // 要复制的通道索引（0=R, 1=G, 2=B, 3=A）
};
```

## 着色器入口点

### `main`
```slang
[numthreads(16, 16, 1)]
void main(uint3 dispatchThreadId: SV_DispatchThreadID)
```

#### 线程组配置
- 16x16x1 线程组
- 每个线程处理一个像素

#### 执行流程
1. 获取线程对应的纹理坐标
2. 边界检查：如果超出视图维度则返回
3. 从源纹理读取指定通道的值
4. 将该值写入目标纹理的所有通道

#### 核心逻辑
```slang
uint2 coord = dispatchThreadId.xy;
if (any(coord >= viewDim))
    return;

// 复制单个颜色通道到目标的所有通道
gDst[coord] = gSrc[coord][channelIndex];
```

## 模板参数

### `TEXTURE_FORMAT`
- 编译时定义的纹理格式
- 支持的值：
  - `float4`：浮点格式纹理
  - `uint4`：无符号整数格式纹理

## 依赖关系

### 被以下文件引用
- `ImageProcessing.cpp`：通过 `ComputePass::create()` 加载

## 实现细节

### 通道索引映射
| channelIndex | 通道 |
|--------------|------|
| 0 | Red (R) |
| 1 | Green (G) |
| 2 | Blue (B) |
| 3 | Alpha (A) |

### 性能特性
- 完全并行化：每个像素独立处理
- 无分支：简单的读写操作
- 内存访问模式：连续访问，缓存友好

### 使用场景
- 提取单个颜色通道
- 通道重排
- 灰度图生成
- Alpha通道分离
