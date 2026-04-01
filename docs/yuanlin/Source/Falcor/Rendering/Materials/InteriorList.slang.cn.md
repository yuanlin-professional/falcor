# InteriorList.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/InteriorList.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

实现内部介质列表（Interior List），用于跟踪光线在嵌套电介质体积内传播时当前所处的材质。通过优先级排序的槽位（slot）管理材质的进入和退出。

## 结构体与接口

### `InteriorList`

- **职责**: 管理嵌套电介质体积的优先级栈

#### 位布局
- bit 0-27: materialID
- bit 28-31: nestedPriority（放在高位便于排序）

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `slots` | `uint[INTERIOR_LIST_SLOT_COUNT]` | 槽位数组（默认 2 个） |

#### 关键函数

| 函数签名 | 说明 |
|----------|------|
| `getTopMaterialID()` | 获取最高优先级材质 ID |
| `getNextMaterialID()` | 获取次高优先级材质 ID |
| `isTrueIntersection(nestedPriority)` | 判断是否为真正的交点 |
| `handleIntersection(materialID, nestedPriority, entering)` | 处理材质进入/退出事件 |
| `sortSlots()` | 使用排序网络对槽位排序 |

## 依赖关系

无外部 import。支持 2-4 个槽位的排序网络实现。
