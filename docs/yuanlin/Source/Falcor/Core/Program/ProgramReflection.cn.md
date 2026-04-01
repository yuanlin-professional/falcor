# ProgramReflection.h / ProgramReflection.cpp 源码文档

> 路径: `Source/Falcor/Core/Program/ProgramReflection.h`, `Source/Falcor/Core/Program/ProgramReflection.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/Program

## 功能概述

提供着色器程序的完整反射系统，用于在运行时查询着色器中声明的变量、类型、资源绑定等信息。该反射系统是 Falcor 参数绑定机制的基础，使得 C++ 代码能够按名称或偏移量访问着色器中的 Uniform 变量、纹理、缓冲区、采样器等资源。反射数据来源于 Slang 编译器的输出。

## 类与接口

### `UniformShaderVarOffset`

- **继承**: 无
- **职责**: 表示 Uniform 着色器变量相对于其所属类型/缓冲区/块的字节偏移

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `explicit UniformShaderVarOffset(size_t offset)` | 从字节偏移构造 |
| `ByteOffset getByteOffset() const` | 获取原始字节偏移 |
| `bool isValid() const` | 检查偏移是否有效 |
| `UniformShaderVarOffset operator+(UniformShaderVarOffset) const` | 偏移叠加 |

---

### `ResourceShaderVarOffset`

- **继承**: 无
- **职责**: 表示资源类型着色器变量的范围索引和数组索引偏移

---

### `ShaderVarOffset`

- **继承**: 无
- **职责**: 组合 Uniform 偏移和资源偏移，表示着色器变量的完整绑定位置

---

### `TypedShaderVarOffset`

- **继承**: `ShaderVarOffset`
- **职责**: 在 `ShaderVarOffset` 基础上附加类型信息

---

### `ReflectionType`

- **继承**: `Object`
- **职责**: 着色器类型的基类，提供类型查询和向下转型接口

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `const ReflectionArrayType* asArrayType() const` | 尝试转为数组类型 |
| `const ReflectionStructType* asStructType() const` | 尝试转为结构体类型 |
| `const ReflectionBasicType* asBasicType() const` | 尝试转为基础类型 |
| `const ReflectionResourceType* asResourceType() const` | 尝试转为资源类型 |
| `const ReflectionInterfaceType* asInterfaceType() const` | 尝试转为接口类型 |
| `const ReflectionType* unwrapArray() const` | 递归展开数组获取元素类型 |
| `size_t getByteSize() const` | 获取类型字节大小 |
| `ShaderVarOffset findMember(std::string_view name) const` | 按名称查找成员偏移 |
| `ShaderVarOffset findMember(uint32_t index) const` | 按索引查找成员偏移 |

---

### `ReflectionArrayType`

- **继承**: `ReflectionType`
- **职责**: 表示着色器中的数组类型

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `uint32_t getElementCount() const` | 获取数组元素数量 |
| `uint32_t getElementByteStride() const` | 获取元素字节步长 |
| `const ref<const ReflectionType>& getElementType() const` | 获取元素类型 |

---

### `ReflectionStructType`

- **继承**: `ReflectionType`
- **职责**: 表示着色器中的结构体类型

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `uint32_t getMemberCount() const` | 获取成员数量 |
| `const ref<const ReflectionVar>& getMember(uint32_t index) const` | 按索引获取成员 |
| `const ref<const ReflectionVar>& getMember(std::string_view name) const` | 按名称获取成员 |
| `const ReflectionVar* findMember(std::string_view name) const` | 按名称查找成员（不抛异常） |
| `std::string_view getName() const` | 获取结构体名称 |

---

### `ReflectionBasicType`

- **继承**: `ReflectionType`
- **职责**: 表示着色器中的基础数据类型（bool、int、uint、float 及其向量/矩阵变体）

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `Type getType() const` | 获取基础类型枚举 |
| `bool isIntType() const` | 是否为整数类型 |
| `bool isUintType() const` | 是否为无符号整数类型 |
| `bool isFloatType() const` | 是否为浮点类型 |
| `bool isBoolType() const` | 是否为布尔类型 |
| `uint32_t getRowCount() const` | 获取矩阵行数 |
| `uint32_t getColCount() const` | 获取矩阵列数 |
| `uint32_t getTotalComponentCount() const` | 获取总分量数 |

---

### `ReflectionResourceType`

- **继承**: `ReflectionType`
- **职责**: 表示着色器中的资源类型（纹理、缓冲区、采样器、加速结构等）

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `Type getType() const` | 获取资源类型（纹理、缓冲区等） |
| `Dimensions getDimensions() const` | 获取资源维度（1D、2D、3D 等） |
| `ShaderAccess getShaderAccess() const` | 获取着色器访问模式（只读/读写） |
| `ReturnType getReturnType() const` | 获取返回类型 |
| `const ref<const ReflectionType>& getStructType() const` | 获取结构化缓冲区的结构类型 |

---

### `ReflectionInterfaceType`

- **继承**: `ReflectionType`
- **职责**: 表示着色器中的接口/存在类型

---

### `ReflectionVar`

- **继承**: `Object`
- **职责**: 表示着色器中的单个变量声明（字段）

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `const std::string& getName() const` | 获取变量名称 |
| `const ref<const ReflectionType>& getType() const` | 获取变量类型 |
| `ShaderVarOffset getBindLocation() const` | 获取绑定位置 |
| `size_t getByteOffset() const` | 获取字节偏移 |

---

### `ParameterBlockReflection`

- **继承**: `Object`
- **职责**: 描述参数块的绑定布局，包括资源范围、根描述符、子对象等

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<ParameterBlockReflection> create(const ProgramVersion*)` | 从程序版本创建 |
| `const ref<const ReflectionType>& getElementType() const` | 获取元素类型 |
| `uint32_t getResourceRangeCount() const` | 获取资源范围数量 |
| `uint32_t getRootDescriptorRangeCount() const` | 获取根描述符范围数量 |
| `uint32_t getParameterBlockSubObjectRangeCount() const` | 获取参数块子对象范围数量 |
| `const ResourceRange& getResourceRange(uint32_t index) const` | 获取指定资源范围 |

---

### `EntryPointGroupReflection`

- **继承**: `ParameterBlockReflection`
- **职责**: 入口点组的反射信息（与 `ParameterBlockReflection` 相同但语义上代表入口点组）

---

### `ProgramReflection`

- **继承**: `Object`
- **职责**: 整个程序的反射信息，包含全局作用域和各入口点组的反射

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<const ProgramReflection> create(...)` | 从 Slang 布局信息创建 |
| `const ref<const ParameterBlockReflection>& getDefaultParameterBlock() const` | 获取默认参数块反射 |
| `ref<const EntryPointGroupReflection> getEntryPointGroup(uint32_t index) const` | 获取入口点组反射 |
| `uint32_t getEntryPointGroupCount() const` | 获取入口点组数量 |

## 依赖关系

### 本文件引用

- `Core/Object.h` — 基类
- `Core/Enum.h` — 枚举工具
- `Core/API/ShaderResourceType.h` — 着色器资源类型定义
- `<slang.h>` — Slang 反射 API

### 被以下文件引用

- `Program.h` — 通过 `getReflector()` 获取反射
- `ProgramVersion.h` — 存储反射对象
- `ProgramVars.h` — 基于反射创建变量
- `ShaderVar.h` — 类型导航
- `Core/API/ParameterBlock.h` — 参数块布局

## 实现细节

- **从 Slang 反射构建**: 实现文件中通过递归遍历 Slang 的 `slang::TypeLayoutReflection` 树来构建 Falcor 自有的反射类型层次结构
- **资源范围扁平化**: `ParameterBlockReflection` 将嵌套的参数块结构扁平化为线性的资源范围列表，便于描述符表的分配
- **D3D12 描述符集布局**: 在 D3D12 后端下，额外生成 `D3D12DescriptorSetLayout` 信息用于根签名构建
- **偏移计算体系**: 使用分离的 Uniform 偏移和资源偏移，以适应 GPU 编程中数据布局和资源绑定的不同寻址方式
- **Python 绑定**: 暴露反射类型查询到 Python 脚本环境
