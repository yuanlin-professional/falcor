# VAO 源码文档

> 路径: `Source/Falcor/Core/API/VAO.h` / `VAO.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

VAO（Vertex Array Object，Vao）封装了顶点数组对象，将图元拓扑、顶点布局、顶点缓冲区和索引缓冲区组合在一起。是图形绘制调用的输入装配阶段配置。

## 类与接口

### `Vao`
- **继承**: `Object`
- **职责**: 管理顶点数组对象

#### 内部枚举 `Topology`
| 值 | 说明 |
|----|------|
| `Undefined` | 未定义 |
| `PointList` | 点列表 |
| `LineList` | 线列表 |
| `LineStrip` | 线带 |
| `TriangleList` | 三角形列表 |
| `TriangleStrip` | 三角形带 |

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `static ref<Vao> create(Topology, ref<VertexLayout>, const BufferVec&, ref<Buffer> pIB, ResourceFormat ibFormat)` | 创建 VAO |
| `uint32_t getVertexBuffersCount() const` | 获取顶点缓冲区数量 |
| `const ref<Buffer>& getVertexBuffer(uint32_t index) const` | 获取指定顶点缓冲区 |
| `const ref<VertexLayout>& getVertexLayout() const` | 获取顶点布局 |
| `ElementDesc getElementIndexByLocation(uint32_t) const` | 按位置查找元素索引 |
| `const ref<Buffer>& getIndexBuffer() const` | 获取索引缓冲区 |
| `ResourceFormat getIndexBufferFormat() const` | 获取索引缓冲区格式 |
| `Topology getPrimitiveTopology() const` | 获取图元拓扑 |

## 依赖关系
### 本文件引用
- `VertexLayout.h`, `Buffer.h`, `Core/Object.h`

### 被以下文件引用
- 图形状态和绘制调用相关模块
