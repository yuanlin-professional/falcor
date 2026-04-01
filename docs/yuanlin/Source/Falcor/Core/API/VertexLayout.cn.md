# VertexLayout 源码文档

> 路径: `Source/Falcor/Core/API/VertexLayout.h` / `VertexLayout.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

VertexLayout 模块定义了顶点缓冲区布局描述，包括单个顶点缓冲区的元素布局（VertexBufferLayout）和多个缓冲区布局的容器（VertexLayout）。

## 类与接口

### `VertexBufferLayout`
- **继承**: `Object`
- **职责**: 描述单个顶点缓冲区的元素布局

#### 内部枚举 `InputClass`
| 值 | 说明 |
|----|------|
| `PerVertexData` | 逐顶点数据 |
| `PerInstanceData` | 逐实例数据 |

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `static ref<VertexBufferLayout> create()` | 创建空布局 |
| `void addElement(name, offset, format, arraySize, shaderLocation)` | 添加元素 |
| `uint32_t getElementOffset(uint32_t index) const` | 获取元素偏移 |
| `ResourceFormat getElementFormat(uint32_t index) const` | 获取元素格式 |
| `const string& getElementName(uint32_t index) const` | 获取元素语义名 |
| `uint32_t getElementShaderLocation(uint32_t index) const` | 获取着色器绑定位置 |
| `uint32_t getStride() const` | 获取总步长 |
| `void setInputClass(InputClass, uint32_t stepRate)` | 设置输入分类和步进率 |

### `VertexLayout`
- **继承**: `Object`
- **职责**: 容纳多个 VertexBufferLayout

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `static ref<VertexLayout> create()` | 创建空布局容器 |
| `void addBufferLayout(uint32_t index, ref<VertexBufferLayout>)` | 添加缓冲区布局 |
| `const ref<VertexBufferLayout>& getBufferLayout(size_t index) const` | 获取指定缓冲区布局 |
| `size_t getBufferCount() const` | 获取缓冲区描述数量 |

## 依赖关系
### 本文件引用
- `Resource.h`, `Core/Object.h`, `Scene/VertexAttrib.slangh`

### 被以下文件引用
- `VAO.h`, `GraphicsStateObject.h`
