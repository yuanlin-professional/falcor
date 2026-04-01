# MaterialParamLayout.slangh 着色器文档
> 路径: `Source/Falcor/Scene/Material/MaterialParamLayout.slangh`
> 类型: Slang 着色器头文件 (.slangh)
> 模块: Scene/Material

## 功能概述
定义了材质参数布局的宏系统 `DEFINE_MATERIAL_PARAM_LAYOUT`，用于在主机端和设备端自动生成参数布局、序列化和反序列化代码。通过 X-Macro 模式，一次定义参数列表，自动生成多种用途的代码。

## 结构体与接口

### 宏定义

#### `DEFINE_MATERIAL_PARAM_LAYOUT(materialName_, params_)`
根据参数列表自动生成：
- **主机端**: 参数布局查询（`layout()`）、序列化（`serialize()`）、反序列化（`deserialize()`）方法
- **设备端**: 参数偏移量常量的结构体

#### 参数宏格式
```
PARAM(type, offset, name, getter, setter, pythonName, [clampMin], [clampMax])
```

### 辅助功能
- `clampMaterialParam()` - 在反序列化时将参数钳制到有效范围
- `kMaterialParamEpsilon = 1e-4` - 默认钳制边界

## 依赖关系
### import（主机端）
- `MaterialParamLayout.h` - 布局数据结构
### import（设备端）
- `Scene.Material.MaterialParamLayout` - GPU 端布局条目

### 被以下文件引用
- `StandardMaterialParamLayout.slang` - 标准材质参数定义
- PBRT 系列材质参数定义
