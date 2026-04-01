# MonitorInfo 源码文档

> 路径: `Source/Falcor/Core/Platform/MonitorInfo.h` + `Source/Falcor/Core/Platform/MonitorInfo.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/Platform

## 功能概述

提供显示器信息查询功能，能够枚举系统中所有连接的显示器并获取其分辨率、物理尺寸、像素密度（PPI）等属性。在 Windows 上通过 EDID 数据和 Win32 API 实现，在 Linux 上通过 GLFW 库实现。

## 类与接口

### `MonitorInfo`

- **继承**: 无
- **职责**: 提取和展示系统显示器的物理参数信息

#### 内部结构体

##### `MonitorDesc`

| 成员 | 类型 | 说明 |
|------|------|------|
| `identifier` | `std::string` | 显示器标识符 |
| `resolution` | `uint2` | 分辨率（像素） |
| `physicalSize` | `float2` | 物理尺寸（英寸） |
| `ppi` | `float` | 像素密度（每英寸像素数） |
| `isPrimary` | `bool` | 是否为主显示器 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static std::vector<MonitorDesc> getMonitorDescs()` | 获取所有连接显示器的描述列表（非线程安全） |
| `static void displayMonitorInfo()` | 将所有显示器信息输出到控制台 |

## 依赖关系

### 本文件引用
- `Core/Macros.h` — 宏定义
- `Utils/Math/Vector.h` — `uint2`、`float2` 向量类型
- `Utils/StringUtils.h` — 字符串转换工具（`wstring_2_string`）
- `<windows.h>` / `<setupapi.h>` / `<cfgmgr32.h>` — Windows 显示器枚举 API
- `Core/GLFW.h` — Linux 下 GLFW 显示器查询

### 被以下文件引用
- 窗口管理、DPI 适配等模块

## 实现细节

- **Windows 实现**:
  - 通过 `EnumDisplayMonitors` 回调函数枚举所有显示器。
  - 使用 `SetupDiGetClassDevsEx` 查找监视器设备，从注册表中读取 EDID 数据以获取物理尺寸（毫米）。
  - 物理尺寸从毫米转换为英寸（除以 25.4）。
  - PPI 计算为水平和垂直像素密度的平均值。
  - 使用全局变量 `internalDescs` 在回调中收集数据（非线程安全）。

- **Linux 实现**:
  - 通过 `glfwGetMonitors` 获取所有显示器。
  - 分辨率取所有视频模式中最大的分辨率。
  - 物理尺寸通过 `glfwGetMonitorPhysicalSize` 获取（毫米转英寸）。
  - 主显示器通过 `glfwGetPrimaryMonitor` 判断。
