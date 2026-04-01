# ModulateIllumination - 光照调制

## 功能概述

ModulateIllumination 是一个光照重组合渲染通道，用于将路径追踪器分解输出的多个光照分量（反射率、辐射度、自发光等）重新调制合并为最终颜色。该通道是延迟渲染管线中光照重建的关键环节，支持以下光照分量的独立开关控制：

- **自发光**（Emission）
- **漫反射**：反射率（Diffuse Reflectance）x 辐射度（Diffuse Radiance）
- **镜面反射**：反射率（Specular Reflectance）x 辐射度（Specular Radiance）
- **Delta 反射**：自发光 + 反射率 x 辐射度（Delta Reflection）
- **Delta 透射**：自发光 + 反射率 x 辐射度（Delta Transmission）
- **残余辐射度**（Residual Radiance）

所有输入均为可选，通过预处理 define 控制有效性。合成公式为：
```
output = emission
       + diffuseReflectance * diffuseRadiance
       + specularReflectance * specularRadiance
       + deltaReflectionEmission + deltaReflectionReflectance * deltaReflectionRadiance
       + deltaTransmissionEmission + deltaTransmissionReflectance * deltaTransmissionRadiance
       + residualRadiance
```

## 架构图

```mermaid
graph TD
    subgraph 输入通道（均为可选）
        E[emission 自发光]
        DR[diffuseReflectance 漫反射率]
        DD[diffuseRadiance 漫辐射度]
        SR[specularReflectance 镜面反射率]
        SD[specularRadiance 镜面辐射度]
        DRE[deltaReflectionEmission]
        DRR[deltaReflectionReflectance]
        DRD[deltaReflectionRadiance]
        DTE[deltaTransmissionEmission]
        DTR[deltaTransmissionReflectance]
        DTD[deltaTransmissionRadiance]
        RR[residualRadiance 残余辐射度]
    end

    E --> CS[ModulateIllumination.cs.slang]
    DR --> CS
    DD --> CS
    SR --> CS
    SD --> CS
    DRE --> CS
    DRR --> CS
    DRD --> CS
    DTE --> CS
    DTR --> CS
    DTD --> CS
    RR --> CS

    CS --> O[output 最终合成颜色]

    UI[renderUI 12 个复选框] -->|控制 define| CS
```

## 文件清单

| 文件名 | 类型 | 说明 |
|--------|------|------|
| `ModulateIllumination.h` | C++ 头文件 | 定义 `ModulateIllumination` 类，包含 12 个光照分量启用标志 |
| `ModulateIllumination.cpp` | C++ 源文件 | 实现通道逻辑：通道声明、define 管理、纹理绑定、UI 渲染 |
| `ModulateIllumination.cs.slang` | Slang 计算着色器 | GPU 端逐像素光照分量合成逻辑 |
| `CMakeLists.txt` | 构建配置 | CMake 构建脚本 |

## 依赖关系

### 框架依赖
- `Falcor.h` - Falcor 核心框架
- `RenderGraph/RenderPass.h` - 渲染通道基类
- `RenderGraph/RenderPassHelpers.h` - 渲染通道辅助工具（通道列表宏、I/O 尺寸计算、有效资源 define 生成）

### 输入/输出通道
| 通道名 | 方向 | 必需 | 说明 |
|--------|------|------|------|
| `emission` | 输入 | 否 | 自发光 |
| `diffuseReflectance` | 输入 | 否 | 漫反射反射率 |
| `diffuseRadiance` | 输入 | 否 | 漫反射辐射度 |
| `specularReflectance` | 输入 | 否 | 镜面反射率 |
| `specularRadiance` | 输入 | 否 | 镜面辐射度 |
| `deltaReflectionEmission` | 输入 | 否 | Delta 反射自发光 |
| `deltaReflectionReflectance` | 输入 | 否 | Delta 反射反射率 |
| `deltaReflectionRadiance` | 输入 | 否 | Delta 反射辐射度 |
| `deltaTransmissionEmission` | 输入 | 否 | Delta 透射自发光 |
| `deltaTransmissionReflectance` | 输入 | 否 | Delta 透射反射率 |
| `deltaTransmissionRadiance` | 输入 | 否 | Delta 透射辐射度 |
| `residualRadiance` | 输入 | 否 | 残余辐射度 |
| `output` | 输出 | 是 | 合成输出（RGBA32Float，UAV） |

### 上游通道依赖
通常接收来自路径追踪器（如 `PathTracer`）分解输出的各光照分量。

## 关键类与接口

### `ModulateIllumination` 类
继承自 `RenderPass`，核心接口：

| 方法 | 说明 |
|------|------|
| `reflect()` | 声明 12 个可选输入通道和 1 个 RGBA32Float 输出通道 |
| `execute()` | 生成有效资源 define 列表 -> 根据 UI 开关覆盖 define -> 绑定纹理 -> 调度着色器 |
| `renderUI()` | 提供 12 个复选框控制各光照分量的启用/禁用 |
| `getProperties()` / 构造函数 | 序列化/反序列化 12 个布尔开关和输出尺寸 |

### 计算着色器（`ModulateIllumination.cs.slang :: main`）
- 线程组大小：`[numthreads(16, 16, 1)]`
- 使用 `#define is_valid(name)` 宏在编译期判断各输入通道是否绑定
- 对有效的辐射度通道乘以对应反射率，累加所有分量得到最终颜色
- 自发光和残余辐射度直接累加，不乘反射率
