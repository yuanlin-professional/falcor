# MaterialParamLayout.slang 着色器文档
> 路径: `Source/Falcor/Scene/Material/MaterialParamLayout.slang`
> 类型: Slang 着色器
> 模块: Scene/Material

## 功能概述
定义了 GPU 端的材质参数布局条目结构 `MaterialParamLayoutEntry`，仅包含偏移量字段。这是 `MaterialParamLayout.slangh` 中宏系统的 GPU 端对应物。

## 结构体与接口

### `MaterialParamLayoutEntry`
| 字段 | 类型 | 说明 |
|------|------|------|
| `offset` | `uint` | 参数在序列化数组中的偏移量 |

## 依赖关系
### 被以下文件引用
- `MaterialParamLayout.slangh` - GPU 端宏展开时引用
