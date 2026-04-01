# Material 源码文档
> 路径: `Source/Falcor/Scene/Material/Material.h` + `Material.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Material

## 功能概述
定义了 Falcor 材质系统的抽象基类 `Material`。所有材质类型（标准材质、布料、头发、测量材质等）均继承自此类。该文件提供了材质的通用接口，包括纹理槽位管理、Alpha 测试模式、折射率设置、双面渲染标志，以及 GPU 数据上传等功能。同时包含 Python 脚本绑定，将材质类型和属性暴露给 Python 脚本系统。

## 类与接口

### `Material`
- **继承**: `Object`
- **职责**: 所有材质类型的抽象基类，定义了材质的通用属性和接口

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `virtual bool renderUI(Gui::Widgets& widget)` | 渲染材质的 UI 界面，返回材质是否被修改 |
| `virtual UpdateFlags update(MaterialSystem* pOwner) = 0` | 更新材质，准备渲染所需数据（纯虚函数） |
| `virtual bool isEqual(const ref<Material>& pOther) const = 0` | 比较两个材质是否相同（除名称外） |
| `virtual bool setTexture(const TextureSlot slot, const ref<Texture>& pTexture)` | 设置指定纹理槽位的纹理 |
| `virtual ref<Texture> getTexture(const TextureSlot slot) const` | 获取指定纹理槽位的纹理 |
| `virtual bool loadTexture(TextureSlot slot, const path& path, bool useSrgb)` | 从文件路径加载纹理 |
| `virtual MaterialDataBlob getDataBlob() const = 0` | 获取材质数据块用于 GPU 上传 |
| `virtual ProgramDesc::ShaderModuleList getShaderModules() const = 0` | 获取材质使用的着色器模块列表 |
| `virtual TypeConformanceList getTypeConformances() const = 0` | 获取材质的类型一致性声明 |
| `void setDoubleSided(bool doubleSided)` | 设置双面渲染标志 |
| `void setAlphaMode(AlphaMode alphaMode)` | 设置 Alpha 测试模式 |
| `void setIndexOfRefraction(float IoR)` | 设置折射率 |
| `ref<BasicMaterial> toBasicMaterial()` | 向下转型为 BasicMaterial |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | GPU 设备引用 |
| `mName` | `std::string` | 材质名称 |
| `mHeader` | `MaterialHeader` | 材质头数据，包含所有材质类型共享的参数 |
| `mTextureTransform` | `Transform` | 纹理变换矩阵 |
| `mTextureSlotInfo` | `array<TextureSlotInfo>` | 纹理槽位信息数组 |
| `mTextureSlotData` | `array<TextureSlotData>` | 纹理槽位数据数组 |
| `mUpdates` | `UpdateFlags` | 更新标志，追踪材质变更 |

### 枚举类型

#### `UpdateFlags`
| 值 | 说明 |
|----|------|
| `None` | 无更新 |
| `CodeChanged` | 着色器代码变更 |
| `DataChanged` | 材质参数变更 |
| `ResourcesChanged` | 纹理/缓冲区/采样器等资源变更 |
| `DisplacementChanged` | 位移贴图参数变更 |
| `EmissiveChanged` | 自发光属性变更 |

#### `TextureSlot`
| 值 | 说明 |
|----|------|
| `BaseColor` | 基础颜色纹理 |
| `Specular` | 高光参数纹理 |
| `Emissive` | 自发光纹理 |
| `Normal` | 法线贴图 |
| `Transmission` | 透射纹理 |
| `Displacement` | 位移贴图 |
| `Index` | 索引纹理（MERLMix 材质专用） |

## 依赖关系
### 本文件引用
- `MaterialData.slang` - 材质数据结构定义
- `TextureHandle.slang` - 纹理句柄
- `MaterialTypeRegistry.h` - 材质类型注册
- `MaterialParamLayout.h` - 材质参数布局
- `SerializedMaterialParams.h` - 序列化材质参数
- `Core/API/Texture.h`, `Sampler.h` - GPU 纹理和采样器
- `Utils/Image/TextureAnalyzer.h` - 纹理分析工具

### 被以下文件引用
- `BasicMaterial.h` - 基础材质子类
- `MERLMaterial.h` - MERL 测量材质
- `MERLMixMaterial.h` - MERL 混合材质
- `RGLMaterial.h` - RGL 测量材质
- `MaterialSystem.h` - 材质系统

## 实现细节
- 构造函数初始化材质头数据，设置默认 Alpha 模式为不透明、Alpha 阈值为 0.5、折射率为 1.0
- `updateTextureHandle` 方法将纹理注册到材质系统的纹理管理器，获取 GPU 端句柄
- `detectNormalMapType` 静态方法根据法线贴图的通道数自动检测法线贴图类型（RG 或 RGB）
- `markUpdates` 方法同时在本地记录更新标志，并通过回调函数通知材质系统
- `prepareDataBlob` 模板方法将材质头和材质特定数据打包成 128 字节的数据块
- 包含完整的 Python 脚本绑定，暴露 `MaterialType`、`AlphaMode`、`TextureSlot` 等枚举和材质属性
