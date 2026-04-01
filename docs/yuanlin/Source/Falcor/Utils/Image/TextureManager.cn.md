# TextureManager 源码文档
> 路径: `Source/Falcor/Utils/Image/TextureManager.h` + `.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Image

## 功能概述
多线程纹理管理器，管理纹理集合并实现异步加载、句柄分配、UDIM纹理支持和引用计数。所有操作线程安全。

## 类与接口

### `TextureManager`
- **继承**: 无
- **职责**: 管理纹理生命周期、句柄分配和异步加载

#### 枚举类型

##### `TextureState`
| 状态 | 说明 |
|------|------|
| `Invalid` | 无效/未知纹理 |
| `Referenced` | 已引用但未加载 |
| `Loaded` | 已完成加载 |

#### 嵌套类型

##### `CpuTextureHandle`
- **成员**:
  - `mID`: 纹理ID（`kInvalidID = uint32_t::max()`）
  - `mIsUdim`: 是否为UDIM纹理
- **方法**:
  - `isValid()`: 检查句柄有效性
  - `getID()`: 获取ID
  - `isUdim()`: 是否为UDIM
  - `toGpuHandle()`: 转换为GPU句柄

##### `Stats` 统计信息
| 字段 | 说明 |
|------|------|
| `textureCount` | 唯一纹理数量 |
| `textureCompressedCount` | 压缩纹理数量 |
| `textureTexelCount` | 总纹素数量 |
| `textureTexelChannelCount` | 总纹素通道数 |
| `textureMemoryInBytes` | 纹理内存占用（字节） |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `TextureManager(pDevice, maxTextureCount, threadCount)` | 构造函数 |
| `addTexture(pTexture)` | 添加已存在的纹理 |
| `loadTexture(path, generateMipLevels, loadAsSRGB, bindFlags, async, importFlags, assetResolver)` | 加载纹理 |
| `loadUdimTexture(...)` | 加载UDIM纹理集 |
| `removeTexture(handle)` | 移除纹理 |
| `getTexture(handle)` | 获取纹理对象 |
| `getTextureDesc(handle)` | 获取纹理描述 |
| `getTextureState(handle)` | 获取纹理状态 |
| `addRef(handle, pObject)` | 增加引用计数 |
| `removeRef(handle, pObject)` | 减少引用计数 |
| `resolveUdimTexture(handle, uv/udimID)` | 解析UDIM纹理 |
| `getStats()` | 获取统计信息 |
| `waitForAllTexturesLoading()` | 等待所有加载完成 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | 设备对象 |
| `mTextureDescs` | `vector<TextureDesc>` | 纹理描述数组 |
| `mFreeList` | `vector<CpuTextureHandle>` | 空闲句柄列表 |
| `mKeyToHandle` | `map<TextureKey, CpuTextureHandle>` | 键到句柄映射 |
| `mTextureToHandle` | `map<const Texture*, CpuTextureHandle>` | 纹理指针到句柄映射 |
| `mUdimIndirection` | `vector<int32_t>` | UDIM间接索引表 |
| `mHandleToObjects` | `map<CpuTextureHandle, Objects>` | 句柄到对象集合映射 |
| `mObjectToHandles` | `map<const Object*, Handles>` | 对象到句柄集合映射 |
| `mAsyncTextureLoader` | `AsyncTextureLoader` | 异步加载器 |
| `mMutex` | `std::mutex` | 互斥锁 |

## 依赖关系

### 本文件引用
- `AsyncTextureLoader.h`
- `Core/API/Texture.h`
- `Core/API/Buffer.h`
- `Core/AssetResolver.h`
- `Scene/Material/TextureHandle.slang`

### 被以下文件引用
- 场景加载器
- 材质系统
- 资源管理器

## 实现细节

### 句柄管理

#### 分配策略
```cpp
CpuTextureHandle addDesc(const TextureDesc& desc) {
    if (!mFreeList.empty()) {
        // 复用空闲句柄
        handle = mFreeList.back();
        mFreeList.pop_back();
        mTextureDescs[handle.getID()] = desc;
    } else {
        // 分配新句柄
        handle = CpuTextureHandle(mTextureDescs.size());
        mTextureDescs.push_back(desc);
    }
    return handle;
}
```

#### 释放策略
- 检查引用计数为0
- 将句柄加入空闲列表
- 从映射表中移除

### UDIM纹理支持

#### UDIM命名约定
- 格式：`basename.<UDIM>.ext`
- UDIM ID范围：1001-1100
- 计算公式：`udimID = 1001 + u + 10*v`，其中 u,v ∈ [0,9]

#### 间接索引表
```cpp
// mUdimIndirection[rangeStart + (udimID - 1001)] = textureID
// 不存在的UDIM用-1标记
```

#### 解析流程
```cpp
CpuTextureHandle resolveUdimTexture(handle, uv) {
    uint32_t udimID = 1001 + uint32_t(uv.x) + 10 * uint32_t(uv.y);
    size_t rangeStart = handle.getID();
    size_t udim = udimID - 1001;
    if (mUdimIndirection[rangeStart + udim] >= 0)
        return CpuTextureHandle(mUdimIndirection[rangeStart + udim]);
    return CpuTextureHandle();  // 无效
}
```

### 异步加载

#### 延迟加载模式
```cpp
setDeferredLoading(true);  // 启用延迟加载
// loadTexture() 立即返回句柄，状态为Referenced
// 纹理在后台线程加载
```

#### 同步加载模式
```cpp
setDeferredLoading(false);  // 禁用延迟加载
// loadTexture() 阻塞直到加载完成
```

#### 等待加载完成
```cpp
waitForAllTexturesLoading();  // 阻塞直到所有加载请求完成
```

### 引用计数

#### 对象关联
- 每个纹理句柄可被多个对象引用
- 每个对象可引用多个纹理句柄
- 双向映射维护引用关系

#### 自动清理
```cpp
removeRef(handle, pObject);
// 如果引用计数降为0，自动移除纹理
```

### 线程安全
- 所有公共方法使用 `std::lock_guard<std::mutex>` 保护
- 异步加载器内部有独立的同步机制
- 支持多线程并发访问

### 统计信息计算
```cpp
Stats getStats() {
    // 遍历所有已加载纹理
    // 累加纹素数、通道数、内存占用
    // 检测压缩格式
    return stats;
}
```

### 性能优化
- 键值缓存避免重复加载相同纹理
- 空闲列表复用句柄ID
- 批量操作减少锁竞争
- 异步加载隐藏IO延迟
