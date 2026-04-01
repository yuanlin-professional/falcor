# ParameterBlock 源码文档

> 路径: `Source/Falcor/Core/API/ParameterBlock.h` / `ParameterBlock.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

ParameterBlock 是着色器参数绑定的核心类，存储与着色器代码中特定类型关联的所有参数数据。支持 Uniform 变量、纹理、缓冲区、采样器、加速结构等各类资源的绑定，并提供 `ShaderVar` 接口进行便捷的参数设置。

## 类与接口

### `ParameterBlock`
- **继承**: `Object`
- **职责**: 管理着色器参数块的创建、资源绑定和数据设置

#### 关键静态工厂方法
| 方法签名 | 说明 |
|----------|------|
| `static ref<ParameterBlock> create(ref<Device>, const ref<const ProgramVersion>&, const ref<const ReflectionType>&)` | 从类型创建 |
| `static ref<ParameterBlock> create(ref<Device>, const ref<const ParameterBlockReflection>&)` | 从反射创建 |
| `static ref<ParameterBlock> create(ref<Device>, const ref<const ProgramVersion>&, const string&)` | 从类型名创建 |

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `gfx::IShaderObject* getShaderObject() const` | 获取 GFX 着色器对象 |
| `template<T> void setVariable(...)` | 设置 Uniform 变量 |
| `void setTexture(...)` | 绑定纹理 |
| `void setBuffer(...)` | 绑定缓冲区 |
| `void setSampler(...)` | 绑定采样器 |
| `void setAccelerationStructure(...)` | 绑定加速结构 |
| `ShaderVar getRootVar()` | 获取根着色器变量 |
| `ShaderVar findMember(const string&)` | 按名称查找成员 |
| `TypedShaderVarOffset getVariableOffset(const string&)` | 获取变量偏移 |

## 依赖关系
### 本文件引用
- `Buffer.h`, `Texture.h`, `Sampler.h`, `RtAccelerationStructure.h`
- `Core/Program/ProgramReflection.h`, `Core/Program/ShaderVar.h`

### 被以下文件引用
- `BlitContext.h`, 以及所有需要设置着色器参数的模块
