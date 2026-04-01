# MaterialInstanceHints.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/MaterialInstanceHints.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

定义创建材质实例时传入的提示标志，控制法线贴图和着色法线调整行为。

## 结构体与接口

### `MaterialInstanceHints`（枚举）

| 标志 | 值 | 说明 |
|------|-----|------|
| `None` | 0 | 无提示 |
| `DisableNormalMapping` | 0x1 | 关闭法线贴图 |
| `AdjustShadingNormal` | 0x2 | 调整着色法线以减少背面命中风险 |

### 辅助函数

| 函数签名 | 说明 |
|----------|------|
| `isNormalMappingEnabled(uint hints)` | 检查法线贴图是否启用 |
| `isAdjustShadingNormalEnabled(uint hints)` | 检查法线调整是否启用 |

## 依赖关系

### include
- `Utils/HostDeviceShared.slangh`
