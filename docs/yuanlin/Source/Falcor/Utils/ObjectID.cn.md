# ObjectID.h 源码文档

> 路径: `Source/Falcor/Utils/ObjectID.h`
> 类型: C++ 头文件（仅头文件）
> 模块: Utils

## 功能概述

提供强类型对象 ID 模板类。通过模板参数区分不同子系统的 ID 类型，避免不同类型 ID 之间的隐式混用。支持有效性检查、类型转换、比较运算和自增操作。

## 类与接口

### `ObjectID<TKindEnum, TKind, TIntType>`

- **继承**: 无
- **模板参数**:
  - `TKindEnum` — 用于区分 ID 种类的枚举类
  - `TKind` — 具体的 ID 种类值
  - `TIntType` — 底层数值类型（默认 `uint32_t`）
- **职责**: 提供类型安全的对象标识符

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `ObjectID()` | 默认构造，创建无效 ID |
| `explicit ObjectID(const T& id)` | 从整数构造，检查范围有效性 |
| `explicit ObjectID(const ObjectID<TKindEnum, TOtherKind, TIntType>& other)` | 同一枚举类不同种类之间的转换 |
| `static ObjectID fromSlang(const T& id)` | 从 Slang 端数值 ID 转换 |
| `static ObjectID Invalid()` | 获取无效 ID |
| `bool isValid() const` | 检查 ID 是否有效 |
| `IntType get() const` | 获取数值 ID |
| `IntType getSlang() const` | 获取用于 Slang 端的数值 ID |
| 比较运算符 `==`、`!=`、`<`、`>`、`<=`、`>=` | ID 比较 |
| `operator++()` / `operator++(int)` | 自增操作 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `kInvalidID` | `constexpr IntType` | 无效 ID 值（类型最大值） |
| `kKind` | `constexpr TKindEnum` | ID 种类标识 |
| `mID` | `IntType` | 底层数值 ID |

## 依赖关系

### 本文件引用

- `Core/Error.h` — 断言宏
- `<fmt/format.h>` — 格式化支持

### 被以下文件引用

- 场景系统（几何 ID、材质 ID 等）
- `ObjectIDPython.h` — Python 绑定支持

## 实现细节

- 提供 `std::hash` 特化以支持作为哈希容器的键
- 提供 `fmt::formatter` 特化以支持格式化输出
- `fromSlang` 和 `getSlang` 方法用于标识 CPU/GPU 边界的 ID 转换点
