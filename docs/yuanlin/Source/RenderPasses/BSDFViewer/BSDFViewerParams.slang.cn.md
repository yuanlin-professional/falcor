# BSDFViewerParams.slang 源码文档

> 路径: `Source/RenderPasses/BSDFViewer/BSDFViewerParams.slang`
> 类型: Slang 着色器头文件
> 模块: RenderPasses/BSDFViewer

## 功能概述

定义 BSDFViewer 在主机和设备之间共享的参数结构体、枚举类型和像素数据回读结构体。

## 结构体与接口

### `BSDFViewerMode`（枚举）

| 枚举值 | 说明 |
|--------|------|
| `Material` | 材质渲染视图（光照球体） |
| `Slice` | 双向散射分布函数切片查看器 |

### `AlbedoSelection`（枚举，位标志）

| 枚举值 | 数值 | 说明 |
|--------|------|------|
| `ShowAlbedo` | 0x1 | 显示反照率而非反射率 |
| `DiffuseReflection` | 0x2 | 包含漫反射反照率 |
| `DiffuseTransmission` | 0x4 | 包含漫透射反照率 |
| `SpecularReflection` | 0x8 | 包含镜面反射反照率 |
| `SpecularTransmission` | 0x10 | 包含镜面透射反照率 |

### `BSDFViewerParams`

| 字段 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `frameDim` | `uint2` | {0,0} | 帧尺寸 |
| `frameCount` | `uint` | 0 | 帧计数 |
| `viewerMode` | `BSDFViewerMode` | Material | 查看模式 |
| `viewportOffset` | `float2` | — | 视口左上角偏移 |
| `viewportScale` | `float2` | — | 视口缩放（1/尺寸） |
| `materialID` | `uint` | 0 | 场景材质 ID |
| `useNormalMapping` | `int` | 0 | 是否使用法线贴图 |
| `useFixedTexCoords` | `int` | 0 | 是否使用固定纹理坐标 |
| `texCoords` | `float2` | {0,0} | 固定纹理坐标 |
| `useDisneyDiffuse` | `int` | 0 | 使用 Disney 漫反射 BRDF |
| `useSeparableMaskingShadowing` | `int` | 0 | 使用可分离遮蔽-阴影函数 |
| `useImportanceSampling` | `int` | 1 | 使用重要性采样 |
| `usePdf` | `int` | 0 | 显式使用采样 pdf |
| `outputAlbedo` | `uint` | 0 | 反照率输出位标志 |
| `enableDiffuse` | `int` | 1 | 启用漫反射叶瓣 |
| `enableSpecular` | `int` | 1 | 启用镜面反射叶瓣 |
| `applyNdotL` | `int` | 0 | 切片模式下乘以 NdotL |
| `useGroundPlane` | `int` | 0 | 绘制地面平面 |
| `useEnvMap` | `int` | 0 | 使用环境贴图 |
| `lightIntensity` | `float` | 1.0 | 光源强度 |
| `lightColor` | `float3` | {1,1,1} | 光源颜色 |
| `useDirectionalLight` | `int` | 0 | 使用方向光 |
| `lightDir` | `float3` | {0,0,-1} | 方向光方向 |
| `orthographicCamera` | `int` | 0 | 使用正交相机 |
| `cameraDistance` | `float` | 1.5 | 透视相机距离 |
| `cameraFovY` | `float` | 90.0 | 垂直视场角（度） |
| `cameraViewportScale` | `float` | — | 运行时计算的视口缩放 |
| `selectedPixel` | `int2` | {0,0} | 选定像素坐标 |

### `PixelData`

像素级回读数据结构体。

| 字段 | 类型 | 说明 |
|------|------|------|
| `texC` | `float2` | 纹理坐标 |
| `T`, `B`, `N` | `float3` | 切线、副切线、法线 |
| `wi`, `wo` | `float3` | 入射/出射方向 |
| `output` | `float3` | 输出颜色值 |
| `guideNormal` | `float3` | 引导法线 |
| `emission` | `float3` | 发光 |
| `roughness` | `float` | 粗糙度 |
| `diffuseReflectionAlbedo` | `float3` | 漫反射反照率 |
| `diffuseTransmissionAlbedo` | `float3` | 漫透射反照率 |
| `specularReflectionAlbedo` | `float3` | 镜面反射反照率 |
| `specularTransmissionAlbedo` | `float3` | 镜面透射反照率 |
| `specularReflectance` | `float3` | 镜面反射率 |
| `isTransmissive` | `int` | 是否为透射材质 |

## 依赖关系 / import

- `Utils/HostDeviceShared.slangh` — 主机/设备共享工具

## 实现细节

- 结构体布局遵循 HLSL 打包规则，使用 `int` 而非 `bool` 确保跨平台一致性。
- `BSDFViewerMode` 和 `AlbedoSelection` 枚举通过 `FALCOR_ENUM_INFO` 和 `FALCOR_ENUM_REGISTER` 注册，支持 UI 绑定。
