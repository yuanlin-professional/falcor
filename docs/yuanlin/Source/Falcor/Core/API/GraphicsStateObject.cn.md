# GraphicsStateObject 源码文档

> 路径: `Source/Falcor/Core/API/GraphicsStateObject.h` / `GraphicsStateObject.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

GraphicsStateObject 封装了完整的图形管线状态对象（PSO），组合了帧缓冲描述、顶点布局、着色器程序、光栅化状态、深度-模板状态和混合状态。

## 类与接口

### `GraphicsStateObjectDesc`（结构体）
| 成员 | 类型 | 说明 |
|------|------|------|
| `fboDesc` | `Fbo::Desc` | 帧缓冲描述 |
| `pVertexLayout` | `ref<const VertexLayout>` | 顶点布局 |
| `pProgramKernels` | `ref<const ProgramKernels>` | 着色器程序内核 |
| `pRasterizerState` | `ref<RasterizerState>` | 光栅化状态 |
| `pDepthStencilState` | `ref<DepthStencilState>` | 深度-模板状态 |
| `pBlendState` | `ref<BlendState>` | 混合状态 |
| `sampleMask` | `uint32_t` | 采样掩码 |
| `primitiveType` | `PrimitiveType` | 图元类型（Point、Line、Triangle、Patch） |

### `GraphicsStateObject`
- **继承**: `Object`
- **职责**: 管理图形管线状态对象

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `gfx::IPipelineState* getGfxPipelineState() const` | 获取 GFX 管线状态 |
| `const GraphicsStateObjectDesc& getDesc() const` | 获取描述符 |
| `gfx::IRenderPassLayout* getGFXRenderPassLayout() const` | 获取渲染通道布局 |

## 依赖关系
### 本文件引用
- `VertexLayout.h`, `FBO.h`, `RasterizerState.h`, `DepthStencilState.h`, `BlendState.h`
- `Core/Program/ProgramVersion.h`

### 被以下文件引用
- `RenderContext.h`/`RenderContext.cpp`（绑定图形管线状态）
