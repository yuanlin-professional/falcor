# USDImporter 源码文档

> 路径: `Source/plugins/importers/USDImporter/USDImporter.h`, `USDImporter.cpp`
> 类型: C++ 头文件 + 实现
> 模块: plugins/importers/USDImporter

## 功能概述

USD（Universal Scene Description）场景文件的导入器插件。支持 `.usd`、`.usda`、`.usdc`、`.usdz` 格式。将 USD 场景层级（网格、曲线、灯光、相机、材质、骨骼动画、点实例化）转换为 Falcor 的 `SceneBuilder` 表示。

## 类与接口

### `USDImporter`

- **继承**: `Importer`
- **插件注册**: `FALCOR_PLUGIN_CLASS`，插件名 `"USDImporter"`
- **支持格式**: usd, usda, usdc, usdz

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static std::unique_ptr<Importer> create()` | 工厂方法 |
| `void importScene(const path&, SceneBuilder&, const map&)` | 打开 USD Stage 并遍历场景图，创建 ImporterContext 后处理所有 prim |

## 依赖关系

### 本文件引用

- `Scene/Importer.h` - 导入器基类
- `ImporterContext.h` - 导入上下文（所有实际导入逻辑）

## 实现细节

- 使用 `UsdStage::Open()` 加载 USD 文件。
- 实际的场景遍历和对象创建逻辑委托给 `ImporterContext`。
- 支持实例代理（instance proxies）调试模式。
