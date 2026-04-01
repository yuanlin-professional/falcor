# MaterialParamLayout 源码文档
> 路径: `Source/Falcor/Scene/Material/MaterialParamLayout.h`
> 类型: C++ 头文件
> 模块: Scene/Material

## 功能概述
定义了材质参数布局的数据结构，用于描述可微渲染中材质参数的名称、偏移量和大小。参数布局信息在主机端和着色器端共享。

## 类与接口

### `MaterialParamLayoutEntry`
- **职责**: 描述单个材质参数的布局信息

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `name` | `const char*` | 参数名称 |
| `pythonName` | `const char*` | Python 端参数名称 |
| `size` | `uint32_t` | 参数大小（以 float 为单位） |
| `offset` | `uint32_t` | 在序列化数组中的偏移量 |

### 类型别名
- `MaterialParamLayout = std::vector<MaterialParamLayoutEntry>`

## 依赖关系
### 被以下文件引用
- `MaterialTypeRegistry.h` - 参数布局查询
- `MaterialParamLayout.slangh` - 布局宏定义
- `Material.h` - 基类接口
