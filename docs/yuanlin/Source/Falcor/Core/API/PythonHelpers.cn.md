# PythonHelpers 源码文档

> 路径: `Source/Falcor/Core/API/PythonHelpers.h` / `PythonHelpers.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

PythonHelpers 提供了 Falcor 与 Python（通过 pybind11）互操作的工具函数，包括 ndarray 大小/连续性检查、数据类型转换、DefineList 和 TypeConformanceList 的 Python 字典转换，以及从 Python kwargs 构建 ProgramDesc。

## 函数

| 函数签名 | 说明 |
|----------|------|
| `size_t getDtypeByteSize(pybind11::dlpack::dtype)` | 获取 dtype 字节大小 |
| `size_t getNdarraySize(const ndarray&)` | 获取 ndarray 元素总数 |
| `size_t getNdarrayByteSize(const ndarray&)` | 获取 ndarray 字节总大小 |
| `size_t isNdarrayContiguous(const ndarray&)` | 检查 ndarray 是否内存连续 |
| `pybind11::dlpack::dtype dataTypeToDtype(DataType)` | Falcor DataType 转 pybind11 dtype |
| `optional<dtype> resourceFormatToDtype(ResourceFormat)` | ResourceFormat 转 pybind11 dtype |
| `pybind11::dict defineListToPython(const DefineList&)` | DefineList 转 Python 字典 |
| `DefineList defineListFromPython(const pybind11::dict&)` | Python 字典转 DefineList |
| `ProgramDesc programDescFromPython(const kwargs&)` | 从 Python kwargs 构建程序描述 |

## 依赖关系
### 本文件引用
- `Core/API/Formats.h`, `Core/Program/Program.h`
- `Utils/Scripting/ScriptBindings.h`, `Utils/Scripting/ndarray.h`

### 被以下文件引用
- `Buffer.cpp`（NumPy/Torch 互操作）、Python 脚本绑定相关文件
