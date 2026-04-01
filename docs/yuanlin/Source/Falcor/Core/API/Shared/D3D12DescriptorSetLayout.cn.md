# D3D12DescriptorSetLayout 源码文档

> 路径: `Source/Falcor/Core/API/Shared/D3D12DescriptorSetLayout.h`
> 类型: C++ 头文件
> 模块: Core/API/Shared

## 功能概述

定义描述符集的布局结构和着色器可见性枚举。`D3D12DescriptorSetLayout` 描述了一个描述符集中包含的描述符范围（类型、寄存器索引、数量、寄存器空间），对应 D3D12 根签名中描述符表的范围定义。`ShaderVisibility` 枚举指定描述符对哪些着色器阶段可见。

## 类与接口

### `ShaderVisibility`（枚举类）
- **职责**: 以位标志形式表示描述符对各着色器阶段的可见性。

| 枚举值 | 说明 |
|--------|------|
| `None` | 不可见 |
| `Vertex` | 顶点着色器可见 |
| `Pixel` | 像素着色器可见 |
| `Hull` | 外壳着色器可见 |
| `Domain` | 域着色器可见 |
| `Geometry` | 几何着色器可见 |
| `Compute` | 计算着色器可见 |
| `All` | 所有着色器阶段可见 |

### `D3D12DescriptorSetLayout`
- **职责**: 定义描述符集中的描述符范围列表和着色器可见性，作为创建描述符集和构建根签名的配置输入。

#### 内部结构

##### `Range`
| 字段 | 类型 | 说明 |
|------|------|------|
| `type` | `ShaderResourceType` | 着色器资源类型（SRV、UAV、CBV、采样器等） |
| `baseRegIndex` | `uint32_t` | 基础寄存器索引 |
| `descCount` | `uint32_t` | 该范围中的描述符数量 |
| `regSpace` | `uint32_t` | 寄存器空间 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `D3D12DescriptorSetLayout(ShaderVisibility visibility = ShaderVisibility::All)` | 构造函数，默认对所有着色器阶段可见 |
| `D3D12DescriptorSetLayout& addRange(ShaderResourceType type, uint32_t baseRegIndex, uint32_t descriptorCount, uint32_t regSpace = 0)` | 添加一个描述符范围，支持链式调用 |
| `size_t getRangeCount() const` | 获取范围数量 |
| `const Range& getRange(size_t index) const` | 获取指定索引的范围 |
| `ShaderVisibility getVisibility() const` | 获取着色器可见性 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mRanges` | `std::vector<Range>` | 描述符范围列表 |
| `mVisibility` | `ShaderVisibility` | 着色器可见性标志 |

## 依赖关系

### 本文件引用
- `Core/Macros.h` — 框架宏定义（包括 `FALCOR_ENUM_CLASS_OPERATORS`）
- `Core/API/Types.h` — 着色器类型枚举（`ShaderType`）
- `Core/API/ShaderResourceType.h` — 着色器资源类型枚举

### 被以下文件引用
- `D3D12DescriptorSet.h` — 使用布局创建描述符集
- `D3D12RootSignature.h` / `.cpp` — 使用布局定义根签名描述符表

## 实现细节

- `ShaderVisibility` 使用位标志模式，每个着色器阶段对应一个位，通过 `FALCOR_ENUM_CLASS_OPERATORS` 宏启用位运算操作符。
- `D3D12DescriptorSetLayout` 是一个轻量级值类型，不继承自 `Object`，可以按值传递和存储。
- `addRange` 方法支持链式调用，方便在一行代码中构建完整的布局定义。
