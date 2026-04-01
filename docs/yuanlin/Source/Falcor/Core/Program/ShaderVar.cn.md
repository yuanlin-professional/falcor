# ShaderVar.h / ShaderVar.cpp 源码文档

> 路径: `Source/Falcor/Core/Program/ShaderVar.h`, `Source/Falcor/Core/Program/ShaderVar.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/Program

## 功能概述

提供着色器变量的指针式访问接口。`ShaderVar` 类似于指向 `ParameterBlock` 内部数据的"指针"，允许通过结构体字段名或数组索引进行导航，并支持设置和获取各种着色器资源（纹理、缓冲区、采样器等）。这是 Falcor 中最常用的着色器参数绑定接口。

## 类与接口

### `ShaderVar`

- **继承**: 无（结构体）
- **职责**: 作为参数块内部着色器变量的轻量级指针，提供导航、赋值和资源绑定功能

#### 构造与状态

| 方法签名 | 说明 |
|----------|------|
| `ShaderVar()` | 默认构造：创建无效/空指针 |
| `ShaderVar(const ShaderVar& other)` | 拷贝构造 |
| `ShaderVar(ParameterBlock* pObject, const TypedShaderVarOffset& offset)` | 指向参数块中特定偏移 |
| `ShaderVar(ParameterBlock* pObject)` | 指向参数块的全部内容 |
| `bool isValid() const` | 检查是否有效 |
| `const ReflectionType* getType() const` | 获取指向的数据类型 |
| `TypedShaderVarOffset getOffset() const` | 获取完整偏移信息 |
| `size_t getByteOffset() const` | 获取字节偏移 |

#### 导航方法

| 方法签名 | 说明 |
|----------|------|
| `ShaderVar operator[](std::string_view name) const` | 按名称访问结构体字段（不存在则抛异常） |
| `ShaderVar operator[](size_t index) const` | 按索引访问数组元素或结构体字段（不存在则抛异常） |
| `ShaderVar findMember(std::string_view name) const` | 按名称查找成员（不存在返回无效 ShaderVar） |
| `ShaderVar findMember(uint32_t index) const` | 按索引查找成员（不存在返回无效 ShaderVar） |
| `bool hasMember(std::string_view name) const` | 检查成员是否存在 |
| `bool hasMember(uint32_t index) const` | 检查索引处成员是否存在 |
| `ShaderVar operator[](const TypedShaderVarOffset&) const` | 按带类型偏移访问 |
| `ShaderVar operator[](const UniformShaderVarOffset&) const` | 按 Uniform 偏移访问（自动查找类型） |

#### 赋值方法

| 方法签名 | 说明 |
|----------|------|
| `void set(const T& val) const` | 设置变量值 |
| `void operator=(const T& val) const` | 赋值运算符（等同于 set） |
| `void setBlob(void const* data, size_t size) const` | 写入原始二进制数据 |

#### 资源绑定方法

| 方法签名 | 说明 |
|----------|------|
| `void setBuffer(const ref<Buffer>&) const` | 绑定缓冲区 |
| `ref<Buffer> getBuffer() const` | 获取绑定的缓冲区 |
| `void setTexture(const ref<Texture>&) const` | 绑定纹理 |
| `ref<Texture> getTexture() const` | 获取绑定的纹理 |
| `void setSrv(const ref<ShaderResourceView>&) const` | 绑定 SRV |
| `ref<ShaderResourceView> getSrv() const` | 获取绑定的 SRV |
| `void setUav(const ref<UnorderedAccessView>&) const` | 绑定 UAV |
| `ref<UnorderedAccessView> getUav() const` | 获取绑定的 UAV |
| `void setAccelerationStructure(const ref<RtAccelerationStructure>&) const` | 绑定加速结构 |
| `ref<RtAccelerationStructure> getAccelerationStructure() const` | 获取绑定的加速结构 |
| `void setSampler(const ref<Sampler>&) const` | 绑定采样器 |
| `ref<Sampler> getSampler() const` | 获取绑定的采样器 |
| `void setParameterBlock(const ref<ParameterBlock>&) const` | 绑定参数块 |
| `ref<ParameterBlock> getParameterBlock() const` | 获取绑定的参数块 |

#### 其他方法

| 方法签名 | 说明 |
|----------|------|
| `void const* getRawData() const` | 获取底层原始数据指针 |
| `operator TypedShaderVarOffset() const` | 隐式转换为带类型偏移 |
| `operator UniformShaderVarOffset() const` | 隐式转换为 Uniform 偏移 |
| `operator ref<Buffer>() const` | 隐式转换为缓冲区 |
| `operator ref<Texture>() const` | 隐式转换为纹理 |
| `operator ref<Sampler>() const` | 隐式转换为采样器 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpBlock` | `ParameterBlock*` | 所指向的参数块（非持有指针） |
| `mOffset` | `TypedShaderVarOffset` | 变量偏移和类型信息 |

## 依赖关系

### 本文件引用

- `ProgramReflection.h` — 反射类型和偏移定义
- `Core/API/Texture.h` — 纹理类型
- `Core/API/Sampler.h` — 采样器类型
- `Core/API/Buffer.h` — 缓冲区类型
- `Core/API/ResourceViews.h` — SRV/UAV
- `Core/API/RtAccelerationStructure.h` — 加速结构
- `Core/API/ParameterBlock.h` — 参数块（头文件末尾包含，.cpp 中使用）

### 被以下文件引用

- 几乎所有渲染通道和场景组件都通过 `ShaderVar` 绑定着色器参数

## 实现细节

- **非持有指针**: `mpBlock` 是裸指针而非智能指针，这是性能优化设计。`ShaderVar` 作为临时对象频繁创建和销毁，引用计数的开销不可忽略。使用者不应长时间持有 `ShaderVar`

- **常量缓冲区自动解引用**: 当 `operator[]` 作用于指向常量缓冲区或参数块的 `ShaderVar` 时，会自动"解引用"进入缓冲区/块的内容进行查找，使得使用者无需关心资源层级

- **数组偏移计算**: 数组元素访问时同时计算 Uniform 偏移（`index * stride`）和资源偏移（`arrayIndex * elementCount + index`），确保嵌套资源的正确绑定

- **UniformShaderVarOffset 反向查找**: 对 `operator[](UniformShaderVarOffset)` 的处理中，需要遍历结构体成员或计算数组索引来重建类型信息，因为 `UniformShaderVarOffset` 不携带类型

- **Python 绑定**: 通过 `__getitem__`/`__setitem__`/`__getattr__`/`__setattr__` 实现 Python 侧的自然访问语法。整数和浮点数赋值使用反射信息确定正确的目标类型（int32/uint32/float）

- **模板特化**: `setImpl` 对 `ref<Texture>`、`ref<Sampler>`、`ref<Buffer>`、`ref<ParameterBlock>` 进行了显式特化，其他类型走通用的 `setBlob` 路径
