# RenderGraphCompiler 源码文档

> 路径: `Source/Falcor/RenderGraph/RenderGraphCompiler.h` + `RenderGraphCompiler.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderGraph (渲染图)

## 功能概述

`RenderGraphCompiler` 负责将渲染图的 DAG 描述编译为可执行的 `RenderGraphExe` 对象。编译过程包括：确定执行顺序（拓扑排序）、编译各渲染通道、自动插入 MSAA 解析通道、验证图合法性以及分配资源。

## 类与接口

### `RenderGraphCompiler`

- **继承**: 无
- **职责**: 编译渲染图，生成执行列表并分配资源。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static std::unique_ptr<RenderGraphExe> compile(RenderGraph&, RenderContext*, const Dependencies&)` | 静态入口，执行完整的编译流程并返回可执行对象 |
| `void resolveExecutionOrder()` | 通过 DFS 和拓扑排序确定渲染通道的执行顺序 |
| `void compilePasses(RenderContext*)` | 编译所有参与执行的渲染通道 |
| `bool insertAutoPasses()` | 自动插入 MSAA 解析通道（ResolvePass） |
| `void allocateResources(ref<Device>, ResourceCache*)` | 为所有渲染通道分配所需资源 |
| `void validateGraph() const` | 验证图的合法性（所有必需输入已满足、至少有一个输出） |
| `void restoreCompilationChanges()` | 恢复编译过程中对原始图所做的临时修改 |
| `RenderPass::CompileData prepPassCompilationData(const PassData&)` | 准备渲染通道编译所需的数据 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mGraph` | `RenderGraph&` | 待编译的渲染图引用 |
| `mpDevice` | `ref<Device>` | GPU 设备 |
| `mDependencies` | `const Dependencies&` | 编译依赖（默认资源属性、外部资源） |
| `mExecutionList` | `std::vector<PassData>` | 编译后的执行列表 |
| `mCompilationChanges` | 匿名结构体 | 记录编译过程中的临时变更 |

### `Dependencies`

| 成员 | 类型 | 说明 |
|------|------|------|
| `defaultResourceProps` | `ResourceCache::DefaultProperties` | 默认资源属性（尺寸、格式） |
| `externalResources` | `ResourceCache::ResourcesMap` | 外部输入资源映射 |

### `PassData`

| 成员 | 类型 | 说明 |
|------|------|------|
| `index` | `uint32_t` | 节点索引 |
| `pPass` | `ref<RenderPass>` | 渲染通道指针 |
| `name` | `std::string` | 渲染通道名称 |
| `reflector` | `RenderPassReflection` | 反射数据 |

## 依赖关系

### 本文件引用

- `RenderGraph.h` - 渲染图
- `RenderPass.h` - 渲染通道基类
- `RenderPassReflection.h` - 渲染通道反射
- `ResourceCache.h` - 资源缓存
- `RenderGraphExe.h` - 图执行器
- `RenderPasses/ResolvePass.h` - MSAA 解析通道
- `Utils/Algorithm/DirectedGraphTraversal.h` - 图遍历算法

### 被以下文件引用

- `RenderGraph.h/cpp` - 渲染图主类

## 实现细节

- **编译流程**: `compile()` 依次执行：注册外部资源 -> 解析执行顺序 -> 编译通道 -> 插入自动通道 -> 验证图 -> 分配资源。
- **执行顺序解析**: 首先标记必要通道（图输出节点和执行依赖边相关节点），然后反向 DFS 找出所有参与通道，最后进行拓扑排序。
- **自动 MSAA 解析**: 当输出为多采样、输入为单采样时，自动插入 `ResolvePass`。如果插入了自动通道，会重新解析执行顺序。
- **资源分配**: 为每个输出字段注册资源，处理输入字段的别名引用。图输出的资源生命周期扩展至整个图执行结束。
- **编译重试**: 如果渲染通道编译失败但反射数据发生变化，会自动重试编译。
- **临时修改恢复**: 编译过程中可能修改原始图（插入自动通道、移除边），编译完成后通过 `restoreCompilationChanges()` 恢复原始状态。
