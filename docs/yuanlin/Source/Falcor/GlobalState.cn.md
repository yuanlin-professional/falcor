# GlobalState 源码文档

> 路径: `Source/Falcor/GlobalState.h` + `Source/Falcor/GlobalState.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Falcor 根模块 — 全局状态管理

## 功能概述

本文件提供 Python 绑定中已废弃的全局状态访问接口。这是一个临时解决方案，用于在 C++ 端重构（消除全局状态如 GPU 设备）的过渡期内保持现有 Python API 的兼容性。主要服务于两个场景：(1) 加载 `.pyscene` 文件时的场景对象创建；(2) 创建/加载渲染图和渲染通道。所有受影响的 Python 绑定标记为 `PYTHONDEPRECATED`，当这些绑定被移除后本文件也将被删除。

## 函数接口

### SceneBuilder 相关

| 函数签名 | 说明 |
|----------|------|
| `void setActivePythonSceneBuilder(SceneBuilder* pSceneBuilder)` | 设置当前活跃的 Python 场景构建器（仅在 `.pyscene` 加载期间有效） |
| `SceneBuilder& accessActivePythonSceneBuilder()` | 获取当前活跃的场景构建器引用。若不在场景构建上下文中，抛出异常 |
| `AssetResolver& getActiveAssetResolver()` | 获取当前活跃的资源解析器。若有场景构建器则使用其解析器，否则返回默认解析器 |

### Device 相关

| 函数签名 | 说明 |
|----------|------|
| `void setActivePythonRenderGraphDevice(ref<Device> pDevice)` | 设置当前活跃的 Python 渲染图设备 |
| `ref<Device> getActivePythonRenderGraphDevice()` | 获取当前活跃的渲染图设备（可能为 nullptr） |
| `ref<Device> accessActivePythonRenderGraphDevice()` | 获取当前活跃的渲染图设备引用。若不在 Mogwai 或渲染图加载上下文中，抛出异常 |

## 依赖关系

### 本文件引用

- `Core/Macros.h` — 宏定义（`FALCOR_API`）
- `Core/AssetResolver.h` — 资源解析器
- `Core/API/Device.h` — GPU 设备
- `Scene/SceneBuilder.h` — 场景构建器
- `Core/Error.h` — 错误处理（`FALCOR_THROW`）

### 被以下文件引用

- Python 绑定中标记为 `PYTHONDEPRECATED` 的模块

## 实现细节

- 使用文件级静态变量 `spActivePythonSceneBuilder` 和 `spActivePythonRenderGraphDevice` 存储全局状态
- 这些全局变量标记有 `// TODO: REMOVEGLOBAL` 注释，表明计划在未来重构中移除
- `access*` 系列函数在指针为空时抛出描述性异常，`get*` 系列函数允许返回空值
