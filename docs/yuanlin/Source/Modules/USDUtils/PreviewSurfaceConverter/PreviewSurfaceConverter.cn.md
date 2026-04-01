# PreviewSurfaceConverter 源码文档

> 路径: `Source/Modules/USDUtils/PreviewSurfaceConverter/PreviewSurfaceConverter.h`, `PreviewSurfaceConverter.cpp`
> 类型: C++ 头文件 + 实现
> 模块: USDUtils / PreviewSurfaceConverter

## 功能概述

将 USD 的 `UsdPreviewSurface` 着色器转换为 Falcor 的 `StandardMaterial` 实例。处理基础颜色、金属度、粗糙度、法线、自发光、不透明度、位移、体积吸收/散射等材质属性。使用 GPU 计算着色器合并纹理通道（如将粗糙度和金属度打包为 ORM 纹理）。

## 类与接口

### `PreviewSurfaceConverter`

- **职责**: 编译所需计算着色器，管理材质缓存，执行 `UsdPreviewSurface` → `StandardMaterial` 的转换。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `PreviewSurfaceConverter(ref<Device> pDevice)` | 构造函数，编译三个计算着色器并创建采样器 |
| `ref<Material> convert(const UsdShadeMaterial&, RenderContext*)` | 将 USD 材质转换为 Falcor 材质，支持缓存去重 |

#### 私有方法

| 方法签名 | 说明 |
|----------|------|
| `StandardMaterialSpec createSpec(...)` | 遍历 UsdPreviewSurface 的所有输入，构建材质规格对象 |
| `ref<Texture> loadTexture(const ConvertedInput&)` | 加载纹理（支持 DDS 和通用格式） |
| `ref<Texture> createSpecularTransmissionTexture(...)` | GPU 计算：将不透明度纹理转换为镜面透射纹理 (1-opacity) |
| `ref<Texture> packBaseColorAlpha(...)` | GPU 计算：将基础颜色和不透明度合并为 RGBA 纹理 |
| `ref<Texture> createSpecularTexture(...)` | GPU 计算：将粗糙度和金属度合并为 ORM 纹理 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | GPU 设备 |
| `mpSpecTransPass` | `ref<ComputePass>` | 不透明度→透射转换着色器 |
| `mpPackAlphaPass` | `ref<ComputePass>` | 基础颜色+Alpha 打包着色器 |
| `mpSpecularPass` | `ref<ComputePass>` | 粗糙度+金属度→ORM 着色器 |
| `mpSampler` | `ref<Sampler>` | 双线性钳位采样器 |
| `mMaterialCache` | `ConvertedMaterialCache<...>` | 材质缓存，避免重复转换 |
| `mMutex` | `std::mutex` | 保护非线程安全操作（纹理创建、计算执行） |

## 依赖关系

### 本文件引用

- `Core/API/Texture.h`、`Sampler.h`、`RenderContext.h` - GPU 资源
- `Core/Pass/ComputePass.h` - 计算通道
- `Scene/Material/StandardMaterial.h` - 标准材质
- `StandardMaterialSpec.h` - 材质规格
- `USDUtils/ConvertedMaterialCache.h` - 材质缓存
- `USDUtils/ConvertedInput.h` - 输入转换
- `Utils/Image/ImageIO.h` - DDS 纹理加载

### 被以下文件引用

- `USDImporter/ImporterContext.h` - 作为 `mpPreviewSurfaceConverter` 成员使用

## 实现细节

- **纹理 Y 翻转**: 纹理在 USD 中为自下而上存储，而 Falcor 假定自上而下寻址，因此始终应用 Y 翻转。
- **不透明度处理**: 根据 `opacityThreshold` 是否 > 0 决定不透明度语义：非零时为裁切（alpha test），零时转换为镜面透射。
- **纹理变换**: 计算 USD 变换的逆变换（USD 变换纹理坐标，Falcor 变换纹理本身）。
- **线程安全**: 纹理加载中图像读取在锁外执行（可并行），仅纹理创建在锁内执行。
- **扩展输入**: 支持标准外的 `volumeAbsorption`、`volumeScattering`、`normal2` 输入。
- 所有材质强制设为双面渲染。
