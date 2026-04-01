# TextureAnalyzer 源码文档
> 路径: `Source/Falcor/Utils/Image/TextureAnalyzer.h` + `.cpp` + `.cs.slang`
> 类型: C++ 头文件 + 实现 + 计算着色器
> 模块: Utils/Image

## 功能概述
纹理内容分析工具，使用GPU并行分析纹理是否为常量颜色、数值范围和统计信息。

## 类与接口

### `TextureAnalyzer`
- **继承**: 无
- **职责**: 分析2D纹理的颜色常量性和数值范围

#### 结果结构体 `Result`

| 字段 | 类型 | 说明 |
|------|------|------|
| `mask` | `uint32_t` | 位0-3：RGBA通道是否变化（0=常量，1=变化）<br>位4-19：每通道数值范围标志（每通道4位）<br>位20-31：保留 |
| `value` | `float4` | 常量颜色值（仅对常量通道有效） |
| `minValue` | `float4` | 最小颜色值（限制为≥0） |
| `maxValue` | `float4` | 最大颜色值（限制为≥0） |

##### `RangeFlags` 枚举
| 标志 | 值 | 说明 |
|------|-----|------|
| `Pos` | 0x1 | 通道有正值 > 0 |
| `Neg` | 0x2 | 通道有负值 < 0 |
| `Inf` | 0x4 | 通道有无穷值 |
| `NaN` | 0x8 | 通道有NaN值 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `TextureAnalyzer(ref<Device>)` | 构造函数 |
| `analyze(pRenderContext, pInput, mipLevel, arraySlice, pResult, resultOffset, clearResult)` | 分析单个纹理 |
| `analyze(pRenderContext, inputs, pResult, clearResult)` | 批量分析多个纹理 |
| `clear(pRenderContext, pResult, resultOffset, resultCount)` | 清除结果缓冲区 |
| `getResultSize()` | 获取结果大小（64字节） |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | 设备对象 |
| `mpClearPass` | `ref<ComputePass>` | 清除通道 |
| `mpAnalyzePass` | `ref<ComputePass>` | 分析通道 |

## 依赖关系

### 本文件引用
- `Core/API/RenderContext.h`
- `Core/Pass/ComputePass.h`
- `Core/API/Buffer.h`
- `Utils/Image/TextureAnalyzer.cs.slang`

### 被以下文件引用
- 纹理优化工具
- 材质系统
- 资源管理器

## 实现细节

### 分析算法

#### 常量检测
```slang
// 读取左上角像素作为参考值
float4 ref = input[uint2(0, 0)];

// 比较当前像素与参考值
mask |= val.x != ref.x ? 1 : 0;  // R通道
mask |= val.y != ref.y ? 2 : 0;  // G通道
mask |= val.z != ref.z ? 4 : 0;  // B通道
mask |= val.w != ref.w ? 8 : 0;  // A通道
```

#### 范围标志计算
```slang
for (int i = 3; i >= 0; i--) {
    if (val[i] > 0.f)  range |= 1;  // Pos
    if (val[i] < 0.f)  range |= 2;  // Neg
    if (isinf(val[i])) range |= 4;  // Inf
    if (isnan(val[i])) range |= 8;  // NaN
    range <<= 4;
}
```

### Wave级优化

#### 并行归约
```slang
// Wave内归约
mask = WaveActiveBitOr(mask);
minVal = WaveActiveMin(minVal);
maxVal = WaveActiveMax(maxVal);

// 首个lane原子写入
if (WaveIsFirstLane()) {
    result.InterlockedOr(resultOffset, mask);
    result.InterlockedMin(resultOffset + 32, asuint(minVal.x));
    result.InterlockedMax(resultOffset + 48, asuint(maxVal.x));
    // ... 其他通道
}
```

### 格式支持

#### 支持的格式类型
- `FormatType::Float`
- `FormatType::Snorm`
- `FormatType::Unorm`
- `FormatType::UnormSrgb`

#### 不支持的格式
- `FormatType::Sint`
- `FormatType::Uint`
- 3D纹理
- 多重采样纹理

### 性能优化

#### 早期退出
```slang
if (WaveActiveAllTrue(!valid))
    return;  // 整个wave都无效时跳过
```

#### 批量处理
- `analyze(vector<Texture>)` 方法一次性分析多个纹理
- 减少CPU-GPU同步开销
- 注意：Falcor会在每次dispatch间插入UAV屏障

### 线程组配置
- 清除通道：256x1x1
- 分析通道：16x16x1

### 结果解析示例
```cpp
Result result;
// 检查R通道是否为常量
bool isConstR = result.isConstant(TextureChannelFlags::Red);
// 检查G通道是否有负值
bool hasNegG = result.isNeg(TextureChannelFlags::Green);
// 获取RGB通道的范围标志
uint32_t rgbRange = result.getRange(TextureChannelFlags::RGB);
```
