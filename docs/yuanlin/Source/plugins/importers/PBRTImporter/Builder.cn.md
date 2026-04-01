# Builder 源码文档

> 路径: `Source/plugins/importers/PBRTImporter/Builder.h`, `Builder.cpp`
> 类型: C++ 头文件 + 实现
> 模块: plugins/importers/PBRTImporter

## 功能概述

pbrt 场景构建器，定义了场景的中间表示（`BasicScene`）和状态机式的构建器（`BasicSceneBuilder`）。`BasicScene` 存储解析后的所有场景实体，`BasicSceneBuilder` 实现 `ParserTarget` 接口，处理 pbrt 命令流并维护图形状态栈。

## 类与接口

### `BasicScene`

- **职责**: 存储完整的 pbrt 场景描述，包括相机、材质、纹理、灯光、形状、实例等。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void setOptions(...)` | 设置全局选项（滤波器、胶片、相机、采样器、积分器、加速器） |
| `void addNamedMaterial(...)` | 添加命名材质 |
| `uint32_t addMaterial(...)` | 添加匿名材质，返回索引 |
| `void addLight/addShapes/addInstances(...)` | 添加灯光、形状、实例 |
| `const MaterialSceneEntity& getMaterial(const MaterialRef&)` | 按引用获取材质（支持索引和名称） |
| `path resolvePath(const path&)` | 解析相对路径 |

### `BasicSceneBuilder`

- **继承**: `ParserTarget`
- **职责**: 实现 pbrt 命令处理接口，维护变换栈和图形状态栈。

#### 关键回调方法

| 方法签名 | 说明 |
|----------|------|
| `onTranslate/onRotate/onScale/onLookAt/onTransform/onConcatTransform` | 变换操作 |
| `onCamera/onFilm/onSampler/onIntegrator/onAccelerator/onPixelFilter` | 全局选项（仅允许在 WorldBegin 前） |
| `onWorldBegin` | 进入世界块，重置变换 |
| `onAttributeBegin/onAttributeEnd` | 属性作用域管理 |
| `onShape/onMaterial/onMakeNamedMaterial/onNamedMaterial` | 形状和材质定义 |
| `onLightSource/onAreaLightSource` | 灯光定义 |
| `onObjectBegin/onObjectEnd/onObjectInstance` | 实例定义和使用 |
| `onEndOfFiles` | 文件结束，提交所有形状和实例 |

### 场景实体类型

| 类型 | 基类 | 说明 |
|------|------|------|
| `SceneEntity` | - | 基本实体（名称、参数、位置） |
| `MaterialSceneEntity` | `SceneEntity` | 材质（含类型） |
| `TransformedSceneEntity` | `SceneEntity` | 带变换的实体 |
| `ShapeSceneEntity` | `TransformedSceneEntity` | 形状（含材质引用、灯光索引、朝向翻转、介质） |
| `InstanceDefinitionSceneEntity` | - | 实例定义（含形状列表） |
| `InstanceSceneEntity` | - | 实例使用（名称 + 变换） |

### `TransformSet`

- **职责**: 支持最多 2 个变换的集合（用于动画变换的起止时间）。

## 依赖关系

### 本文件引用

- `Types.h` - 基本类型定义
- `Parser.h` - `ParserTarget`、`ParsedParameterVector`
- `Helpers.h` - 错误/警告辅助函数

### 被以下文件引用

- `PBRTImporter.cpp` - 创建 `BasicScene` 和 `BasicSceneBuilder`

## 实现细节

- 使用 `VERIFY_OPTIONS` / `VERIFY_WORLD` 宏确保命令在正确的块中调用。
- pbrt 矩阵为行向量格式，导入时进行转置。
- 支持命名坐标系统（`CoordinateSystem`/`CoordSysTransform`）。
- 动画变换（`TransformTimes`）被解析但标记为不支持。
- 图形状态使用栈管理，支持 `AttributeBegin`/`End` 和 `ObjectBegin`/`End` 嵌套。
