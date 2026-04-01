# Plugin 源码文档

> 路径: `Source/Falcor/Core/Plugin.h` + `Source/Falcor/Core/Plugin.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core

## 功能概述

实现 Falcor 的插件管理系统。支持从动态链接库（DLL/SO）加载插件，注册插件类，并在运行时按类型名创建插件实例。插件系统基于"插件基类"和"插件类"的两层架构设计。

## 类与接口

### `PluginManager`
- **职责**: 全局插件管理器（单例），负责加载/释放插件库和创建插件实例

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `instance()` | 获取单例实例 |
| `createClass<BaseT>(type, args...)` | 按类型名创建插件实例 |
| `hasClass<BaseT>(type)` | 检查指定类型的插件是否已注册 |
| `getInfos<BaseT>()` | 获取所有已注册插件的信息列表 |
| `loadPluginByName(name)` | 按名称加载插件库（自动确定路径和扩展名） |
| `loadPlugin(path)` | 从指定路径加载插件库 |
| `releasePlugin(path)` | 释放指定插件库 |
| `loadAllPlugins()` | 从 `plugins.json` 加载所有插件 |
| `releaseAllPlugins()` | 释放所有已加载的插件 |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `mLibraries` | `map<path, SharedLibraryHandle>` | 已加载的插件库映射 |
| `mClassDescs` | `map<string, shared_ptr<ClassDescBase>>` | 已注册的插件类描述映射 |

### `PluginRegistry`
- **职责**: 辅助类，传递给插件库的 `registerPlugin` 函数用于注册插件类

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `registerClass<BaseT>(type, info, create)` | 注册插件类（完整参数版本） |
| `registerClass<BaseT, T>()` | 注册插件类（自动从 T 获取类型名、信息和创建函数） |

## 宏定义

| 宏 | 说明 |
|------|------|
| `FALCOR_PLUGIN_BASE_CLASS(cls)` | 扩展类作为插件基类，添加 `getPluginBaseType`、`getPluginType`、`getPluginInfo` 接口 |
| `FALCOR_PLUGIN_CLASS(cls, type, info)` | 扩展类作为插件实现类，定义静态类型名和信息 |

## 依赖关系

### 本文件引用
- `Core/Macros.h` — 基础宏
- `Core/Error.h` — 异常处理
- `Core/Platform/OS.h` — 共享库加载函数（`loadSharedLibrary`、`getProcAddress`、`releaseSharedLibrary`、`getRuntimeDirectory`）
- `Utils/Logger.h` — 日志
- `Utils/Timing/CpuTimer.h` — 加载计时
- `nlohmann/json.hpp` — 解析 `plugins.json`

### 被以下文件引用
- `Core/SampleApp.cpp` — 加载和释放所有插件
- 各渲染通道插件库

## 实现细节

- 插件库必须导出 `registerPlugin(PluginRegistry&)` 函数。
- `loadPlugin` 使用互斥锁保证线程安全，避免重复加载。
- `loadAllPlugins` 从 `plugins.json` 读取插件名列表并依次加载，记录总加载耗时。
- `releasePlugin` 在释放共享库前先删除该库注册的所有插件类描述。
- `releaseAllPlugins` 逐个释放，避免持锁期间调用可能死锁的函数。
