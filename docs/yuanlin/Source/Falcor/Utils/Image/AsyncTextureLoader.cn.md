# AsyncTextureLoader 源码文档
> 路径: `Source/Falcor/Utils/Image/AsyncTextureLoader.h` + `.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Image

## 功能概述
多线程异步纹理加载工具类，使用工作线程池并行加载纹理，定期同步GPU上传以控制上传堆大小。

## 类与接口

### `AsyncTextureLoader`
- **继承**: 无
- **职责**: 管理纹理异步加载的线程池和请求队列

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `AsyncTextureLoader(ref<Device>, size_t threadCount)` | 构造函数，创建指定数量的工作线程 |
| `~AsyncTextureLoader()` | 析构函数，等待所有线程终止并同步GPU |
| `loadFromFile(...)` | 从单个文件异步加载纹理 |
| `loadMippedFromFiles(...)` | 从多个文件加载多级mipmap纹理 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | 设备对象 |
| `mThreads` | `std::vector<std::thread>` | 工作线程池 |
| `mLoadRequestQueue` | `std::queue<LoadRequest>` | 加载请求队列 |
| `mMutex` | `std::mutex` | 同步互斥锁 |
| `mCondition` | `std::condition_variable` | 条件变量 |
| `mFlushBarrier` | `std::shared_ptr<Barrier>` | 刷新同步屏障 |
| `mTerminate` | `bool` | 终止标志 |
| `mFlushPending` | `bool` | 刷新挂起标志 |
| `mUploadCounter` | `uint32_t` | 上传计数器 |

### `LoadRequest` 结构体
- **成员**:
  - `paths`: 纹理文件路径列表
  - `generateMipLevels`: 是否生成mipmap
  - `loadAsSRGB`: 是否加载为sRGB格式
  - `bindFlags`: 资源绑定标志
  - `importFlags`: 导入标志
  - `callback`: 加载完成回调函数
  - `promise`: 用于返回结果的promise对象

## 依赖关系

### 本文件引用
- `Core/API/Device.h`
- `Core/API/Texture.h`
- `Utils/Threading.h`
- `Bitmap.h`

### 被以下文件引用
- `TextureManager.h`
- 场景加载器
- 资源管理系统

## 实现细节

### 线程池架构
- 使用生产者-消费者模式
- 工作线程在条件变量上等待新任务
- 支持动态配置线程数量（默认为硬件并发数）

### 上传堆管理
- 每16次纹理上传后触发全局GPU刷新（`kUploadsPerFlush = 16`）
- 使用屏障同步所有工作线程
- 防止上传堆无限增长

### 同步机制
```cpp
// 工作线程等待逻辑
mCondition.wait(lock, [&]() {
    return mTerminate || !mLoadRequestQueue.empty() || mFlushPending;
});
```

### 异步接口
- 返回 `std::future<ref<Texture>>` 供调用者查询加载状态
- 支持可选的回调函数在加载完成时执行
- 加载失败时返回 `nullptr`

### 生命周期管理
- 析构时设置终止标志并通知所有线程
- 等待所有线程join完成
- 最后同步GPU确保所有上传完成
