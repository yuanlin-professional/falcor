# TextureAnalyzer.cs.slang 源码文档
> 路径: `Source/Falcor/Utils/Image/TextureAnalyzer.cs.slang`
> 类型: Slang 计算着色器
> 模块: Utils/Image

## 功能概述
GPU计算着色器，用于分析纹理内容的常量性、数值范围和统计信息，使用Wave级并行归约优化性能。

## 结构体与接口

### `TextureAnalyzer` 结构体
```slang
struct TextureAnalyzer {
    Texture2D<float4> input;           // 输入纹理
    RWByteAddressBuffer result;        // 结果缓冲区
    uint32_t resultOffset;             // 结果偏移量（字节）
    uint2 inputDim;                    // 输入纹理维度
};
```

#### 方法

##### `clear(uint index)`
- **功能**: 清除结果缓冲区
- **参数**: `index` - 要清除的结果索引
- **布局**:
  - 偏移+0: mask (uint4, 16字节)
  - 偏移+16: value (float4, 16字节)
  - 偏移+32: minValue (float4, 16字节)
  - 偏移+48: maxValue (float4, 16字节)

##### `analyze(uint2 texel)`
- **功能**: 分析单个纹素
- **参数**: `texel` - 纹素坐标
- **输出**: 更新结果缓冲区

## 着色器入口点

### `clear` 入口点
```slang
[numthreads(256, 1, 1)]
void clear(uint3 dispatchThreadId: SV_DispatchThreadID)
```
- **线程组**: 256x1x1
- **功能**: 并行清除多个结果槽

### `analyze` 入口点
```slang
[numthreads(16, 16, 1)]
void analyze(uint3 dispatchThreadId: SV_DispatchThreadID)
```
- **线程组**: 16x16x1
- **功能**: 并行分析纹理像素

## 常量缓冲区
```slang
ConstantBuffer<TextureAnalyzer> gTextureAnalyzer;
```

## 依赖关系

### 本文件引用
- `Utils/Math/MathConstants.slangh`

### 被以下文件引用
- `TextureAnalyzer.cpp`：通过 `ComputePass::create()` 加载

## 实现细节

### 分析算法

#### 常量检测
```slang
float4 ref = input[uint2(0, 0)];  // 参考值（左上角像素）
float4 val = input[texel];

// 生成位掩码（位0-3表示RGBA通道是否变化）
mask |= val.x != ref.x ? 1 : 0;
mask |= val.y != ref.y ? 2 : 0;
mask |= val.z != ref.z ? 4 : 0;
mask |= val.w != ref.w ? 8 : 0;
```

#### 范围标志
```slang
// 每个通道4位标志（位4-19）
// 格式: | 0...0 | A | B | G | R | 0000 |
for (int i = 3; i >= 0; i--) {
    if (val[i] > 0.f)  range |= 1;  // 正值
    if (val[i] < 0.f)  range |= 2;  // 负值
    if (isinf(val[i])) range |= 4;  // 无穷
    if (isnan(val[i])) range |= 8;  // NaN
    range <<= 4;
}
mask |= range;
```

### Wave级优化

#### 并行归约
```slang
// 提前退出：整个wave都无效
if (WaveActiveAllTrue(!valid))
    return;

// Wave内归约操作
mask = WaveActiveBitOr(mask);      // 按位或
minVal = WaveActiveMin(minVal);    // 最小值
maxVal = WaveActiveMax(maxVal);    // 最大值
```

#### 原子写入
```slang
if (WaveIsFirstLane()) {
    // 只有wave的第一个lane执行原子操作
    result.InterlockedOr(resultOffset, mask);

    // 限制为非负值（因为使用整数原子操作）
    minVal = max(minVal, float4(0));
    maxVal = max(maxVal, float4(0));

    // 每个通道的最小值
    result.InterlockedMin(resultOffset + 32, asuint(minVal.x));
    result.InterlockedMin(resultOffset + 36, asuint(minVal.y));
    result.InterlockedMin(resultOffset + 40, asuint(minVal.z));
    result.InterlockedMin(resultOffset + 44, asuint(minVal.w));

    // 每个通道的最大值
    result.InterlockedMax(resultOffset + 48, asuint(maxVal.x));
    result.InterlockedMax(resultOffset + 52, asuint(maxVal.y));
    result.InterlockedMax(resultOffset + 56, asuint(maxVal.z));
    result.InterlockedMax(resultOffset + 60, asuint(maxVal.w));
}
```

### 结果布局（64字节）

| 偏移 | 大小 | 字段 | 说明 |
|------|------|------|------|
| 0 | 4 | mask | 常量性和范围标志 |
| 4 | 12 | reserved | 保留 |
| 16 | 16 | value | 参考值（float4） |
| 32 | 16 | minValue | 最小值（float4） |
| 48 | 16 | maxValue | 最大值（float4） |

### 性能特性

#### Wave级并行
- 利用GPU的wave/warp级并行原语
- 减少原子操作次数（每个wave只写一次）
- 提高内存带宽利用率

#### 早期退出
- 检测整个wave是否都在边界外
- 避免不必要的计算和内存访问

#### 内存访问优化
- 使用字节地址缓冲区（`RWByteAddressBuffer`）
- 原子操作确保多线程安全
- 浮点数通过 `asuint()` 转换为整数进行原子比较
