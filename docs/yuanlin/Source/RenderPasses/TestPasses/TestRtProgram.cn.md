# TestRtProgram 源码文档

> 路径: `Source/RenderPasses/TestPasses/TestRtProgram.h` + `TestRtProgram.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/TestPasses

## 功能概述

TestRtProgram 是一个用于测试 Falcor 光线追踪程序（RtProgram）功能的渲染通道。它提供两种测试模式：

- **模式 0**: 测试两种不同光线类型对三角形网格和自定义图元（球体交叉着色器）的追踪，以及每个几何体独立的命中组分配。
- **模式 1**: 测试使用不同类型一致性（TypeConformance）对命中组进行特化，模拟 `createDynamicObject()` 的材质系统。

还支持动态添加、移除和移动自定义图元（球体）。

## 类与接口

### `TestRtProgram`

- **继承**: `RenderPass`
- **职责**: 测试光线追踪程序的各种配置

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `TestRtProgram(ref<Device>, const Properties&)` | 构造函数，解析模式参数 |
| `Properties getProperties() const` | 序列化模式参数 |
| `RenderPassReflection reflect(const CompileData&)` | 声明输出 `output`（RGBA32Float） |
| `void setScene(RenderContext*, const ref<Scene>&)` | 设置场景并初始化光线追踪程序 |
| `void execute(RenderContext*, const RenderData&)` | 检测场景变化、执行光线追踪 |
| `void renderUI(Gui::Widgets&)` | 显示模式、自定义图元管理 UI |
| `static void registerScriptBindings(pybind11::module&)` | 注册 addCustomPrimitive、removeCustomPrimitive、moveCustomPrimitive |

#### 私有方法

| 方法签名 | 说明 |
|----------|------|
| `void sceneChanged()` | 场景变化时重建光线追踪程序和着色器绑定表 |
| `void addCustomPrimitive()` | 在随机位置添加球体自定义图元 |
| `void removeCustomPrimitive(uint32_t)` | 移除指定索引的自定义图元 |
| `void moveCustomPrimitive()` | 随机移动一个自定义图元 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpScene` | `ref<Scene>` | 当前场景 |
| `mMode` | `uint32_t` | 测试模式（0 或 1） |
| `mSelectedIdx` | `uint32_t` | UI 中选中的自定义图元索引 |
| `mUserID` | `uint32_t` | 下一个自定义图元的用户 ID |
| `mSelectedAABB` | `AABB` | 选中图元的轴对齐包围盒 |
| `mRT.pProgram` | `ref<Program>` | 光线追踪程序 |
| `mRT.pVars` | `ref<RtProgramVars>` | 光线追踪程序变量 |

## 依赖关系

### 本文件引用

- `Falcor.h`
- `RenderGraph/RenderPass.h`
- `TestRtProgram.rt.slang`（光线追踪着色器）

### 被以下文件引用

- `TestPasses.cpp`（插件注册）

## 实现细节

1. **模式 0 SBT 配置**: 创建 2 种光线类型 x 2 个 miss 着色器的绑定表。三角形几何体使用 defaultMtl0/1 作为默认命中组，特定几何体覆盖为 green/red。自定义图元使用球体交叉着色器，偶数 userID 的图元使用 purple/yellow 材质。
2. **模式 1 类型特化**: 创建三种类型一致性（Mtl0、Mtl1、Mtl2），对应 `IMtl` 接口的三种实现。根据几何体 ID 选择不同的特化版本。
3. **场景更新检测**: 检查 `RecompileNeeded` 和 `GeometryChanged` 标志来决定是否需要重建光线追踪程序。
