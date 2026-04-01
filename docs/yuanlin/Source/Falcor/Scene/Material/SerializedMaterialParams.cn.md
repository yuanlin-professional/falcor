# SerializedMaterialParams 源码文档
> 路径: `Source/Falcor/Scene/Material/SerializedMaterialParams.h`
> 类型: C++ 头文件
> 模块: Scene/Material

## 功能概述
定义了序列化材质参数的容器结构，用于可微渲染中材质参数的序列化和反序列化。提供类型安全的读写方法，支持标量和向量参数。

## 类与接口

### `SerializedMaterialParams`
- **继承**: `std::array<float, 20>`
- **职责**: 固定大小的材质参数序列化容器

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `void write(float value, size_t offset)` | 写入标量参数 |
| `void write(vector<float, N> value, size_t offset)` | 写入向量参数 |
| `void read(float& value, size_t offset) const` | 读取标量参数 |
| `void read(vector<float, N>& value, size_t offset) const` | 读取向量参数 |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `kParamCount` | `constexpr size_t` | 参数总数 = 20 |

## 依赖关系
### 被以下文件引用
- `Material.h` - 材质基类序列化接口
- `StandardMaterialParamLayout.slangh` - 序列化/反序列化宏
