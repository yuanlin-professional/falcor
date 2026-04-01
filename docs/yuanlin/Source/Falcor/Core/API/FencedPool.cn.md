# FencedPool 源码文档

> 路径: `Source/Falcor/Core/API/FencedPool.h`
> 类型: C++ 头文件（模板类）
> 模块: Core/API

## 功能概述

FencedPool 是一个基于 Fence 同步的对象池模板类。它利用 GPU Fence 追踪对象的使用状态，当对象关联的 GPU 工作完成后自动回收对象，避免频繁创建和销毁。

## 类与接口

### `FencedPool<ObjectType>`
- **继承**: `Object`
- **职责**: 基于 Fence 的资源对象池管理

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `static ref<FencedPool> create(ref<Fence>, NewObjectFuncType, void*)` | 创建对象池 |
| `ObjectType newObject()` | 获取一个可用对象（回收已完成的或创建新的） |

## 实现细节

- 内部维护一个按时间戳排序的队列 `mQueue`
- `newObject()` 将当前活动对象与 Fence 值一起入队
- 检查队首对象的时间戳是否小于 Fence 当前值来判断是否可回收
- 使用用户提供的工厂函数 `NewObjectFuncType` 创建新对象

## 依赖关系
### 本文件引用
- `Fence.h`, `Core/Macros.h`, `Core/Error.h`, `Core/Object.h`
