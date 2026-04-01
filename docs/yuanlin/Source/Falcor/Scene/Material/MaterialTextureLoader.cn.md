# MaterialTextureLoader 源码文档
> 路径: `Source/Falcor/Scene/Material/MaterialTextureLoader.h` + `MaterialTextureLoader.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Material

## 功能概述
材质纹理异步加载辅助类。通过纹理管理器异步加载纹理，在所有纹理加载完成后统一分配给对应材质，避免逐个加载纹理导致的性能问题。

## 类与接口

### `MaterialTextureLoader`
- **继承**: 无
- **职责**: 批量异步加载材质纹理并延迟分配

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `MaterialTextureLoader(TextureManager&, bool useSrgb)` | 构造函数 |
| `~MaterialTextureLoader()` | 析构函数，自动完成纹理分配 |
| `void loadTexture(const ref<Material>&, TextureSlot, const path&)` | 请求异步加载纹理 |
| `void finishLoading()` | 等待加载完成并分配纹理 |

## 依赖关系
### 本文件引用
- `Material.h`, `TextureManager.h`

## 实现细节
- 析构函数中自动调用 `assignTextures()`，确保所有纹理在对象销毁前分配完成
- `loadTexture()` 根据纹理槽位的 sRGB 标志决定是否以 sRGB 格式加载
- 使用 `TextureManager::loadTexture()` 的异步模式发起加载请求
