### [Index](../index.md) | [Usage](./index.md) | Render Pass

--------

# Render Pass

注意：本页面将涵盖 render pass 的更复杂元素。基本元素在[此处](../tutorials/02-implementing-a-render-pass.md)的教程中介绍。

## 注册类

Falcor 将在您的共享库中查找名为 `registerPlugin()` 的函数。其签名如下：

```c++
extern "C" FALCOR_API_EXPORT void registerPlugin(Falcor::PluginRegistry& registry);
```

此函数应注册库导出的所有 render pass 类：

```c++
extern "C" FALCOR_API_EXPORT void registerPlugin(Falcor::PluginRegistry& registry)
{
    registry.registerClass<RenderPass, ExamplePass>();
}
```

通常，`registerPlugin()` 与它导出的 render pass 位于同一源文件中。对于导出多个 pass 的库，此函数应位于与项目同名的单独源文件中（例如，名为 `Antialiasing` 的库如果包含若干 render pass，应将这些函数放在源文件 `Antialiasing.cpp` 中）。

## Render Pass 资源分配与生命周期

默认情况下，每个输出和临时资源都由渲染图分配。输入资源永远不会专门为某个 pass 分配。
输入资源必须通过图中的边或用户直接绑定到图的资源来满足。

资源可以标记 `Field::Flags::Optional` 位。此标志仅对输入和输出资源有意义。在内部资源上使用此字段是确保它不会被分配的好方法。
对于输入资源，此标志意味着即使此输入未被满足，pass 也可以执行。
如果在输出资源上设置此标志，则仅在图边需要时才会分配该资源。

在资源上使用 `Field::Flags::Persistent` 位告诉图系统该资源需要在 `RenderPass::execute()` 调用之间保留其数据。这实际上禁用了渲染图对当前资源执行的所有资源分配优化。
* *注意，此标志不能确保跨图重编译的持久性。重编译几乎肯定会重置资源。*

最后需要注意的是，您不应该在 pass 内缓存资源。这将干扰渲染图分配器，并可能导致渲染错误。

## 在 Pass 之间传递数据

### 渲染数据

每个 render pass 指定它需要的输入/输出。这些数据随后可在 pass 的 `execute()` 函数中通过 `pRenderData->getTexture()` 获取。

### 其他数据

除渲染数据外的其他数据可以通过 `InternalDictionary` 在 pass 之间传递，该字典可在 `execute()` 中通过 `pRenderData->getDictionary()` 访问。

`InternalDictionary` 是一个共享资源，所有 pass 都可以对其进行读/写。存储在其中的数据在帧之间是持久的，但不会跨渲染图重编译持久。

Pass A：
```
float x = 5.0f
InternalDictionary& dict = pRenderData->getDictionary();
dict["foo"] = x;    // 为键 'foo' 存储 float 值
```

Pass B：
```
InternalDictionary& dict = pRenderData->getDictionary();
float x = dict["foo"];    // 从键 'foo' 加载 float 值
```

如果在尝试加载时键不存在，将记录一个错误。可以使用 `keyExists()` 函数在加载前检查键是否存在。

## 序列化 Pass

### 加载 pass

您必须提供的 `create()` 方法接受一个 `Dictionary` 对象。这本质上是一个带有 Python 绑定的映射，其中键是字符串，值可以是任意对象。

渲染图导入器将解析该字典并将其传递给 render pass 的 `create()`。

pass 负责根据 `Dictionary` 中找到的键/值对初始化其成员。

在加载时将字典中的值转换为另一种类型在某些情况下会失败。这也是一个错误。对于此类类型，先加载到临时变量中，然后在单独的步骤中进行转换：
```
float tmp = dict["foo"];
mytype x = (mytype)tmp;
```

### 保存 pass

渲染图导出器将调用虚方法 `getScriptingDictionary()`。使用此函数来序列化您的 pass，如下所示：

```c++
const char* kFilter = "filter"
Dictionary ExamplePass::getScriptingDictionary() const
{
    Dictionary dict;
    dict[kFilter] = mFilter;
    return dict;
}
```

您可能需要对脚本绑定进行一些更改，即使在没有声明新类或枚举的情况下也是如此。请参见下文。

## 脚本绑定

为了将您的 pass 与 Python 脚本一起使用，您需要将其以及任何关联的类、函数、枚举和属性注册到 pybind11。操作方式如下：

```c++
static void regExamplePass(pybind11::module& m)
{
    // 在此注册类、属性和枚举
}

extern "C" FALCOR_API_EXPORT void registerPlugin(Falcor::PluginRegistry& registry)
{
    registry.registerClass<RenderPass, ExamplePass>();
    ScriptBindings::registerBinding(regExamplePass);
}
```

### 注册类

如果您计划暴露与 render pass 关联的属性或函数，必须将 render pass 类注册到脚本模块：

```c++
static void regExamplePass(pybind11::module& m)
{
    pybind11::class_<ExamplePass, RenderPass, ExamplePass::SharedPtr> pass(m, "ExamplePass");
    ...
}
```

这里我们指定 `ExamplePass` 继承自 `RenderPass` 并使用 `ExamplePass::SharedPtr` 作为持有者类型（Falcor 使用共享指针管理资源）。

如果您有其他类希望注册到脚本模块：

```c++
static void regExamplePass(pybind11::module& m)
{
    pybind11::class_<SomeClass> someClass(m, "SomeClass");
    ...
}
```

### 注册属性

如果您想将属性暴露给 Python 脚本：

```c++
static void regExamplePass(pybind11::module& m)
{
    pybind11::class_<ExamplePass, RenderPass, ExamplePass::SharedPtr> pass(m, "ExamplePass");
    pass.def_property("property", &SomeClass::getProperty, &SomeClass::setProperty);
    pass.def_property_readonly("readOnlyProperty", &SomeClass::getReadOnlyProperty);
}
```

这允许您像访问任何 Python 类实例的属性一样访问您的 pass 或其他类的绑定属性。

### 注册方法

如果您想将方法暴露给 Python 脚本：

```c++
static void regExamplePass(pybind11::module& m)
{
    pybind11::class_<ExamplePass, RenderPass, ExamplePass::SharedPtr> pass(m, "ExamplePass");
    pass.def("foo", &ExamplePass::foo); // 简单函数注册
    pass.def("bar", &ExamplePass::bar, "someArg"_a = 1); // 带命名/默认参数的函数
    pass.def("baz", pybind11::overload_cast<uint32_t>(&ExamplePass::baz));
    pass.def("baz", pybind11::overload_cast<>(&ExamplePass::baz); // 重载函数
}
```

需要注意的几点：
- `""_a` 运算符将提供的字符串转换为具有该名称的 `pybind11::arg`。
- `pybind11::overload_cast<>()` 的模板参数是要绑定的重载函数的参数类型。例如，`ExamplePass::baz` 的第一个绑定绑定的是接受 `uint32_t` 的重载，而第二个绑定绑定的是不接受参数的重载。

### 注册枚举

如果您有枚举需要注册：

```c++
static void regExamplePass(pybind11::module& m)
{
    pybind11::enum_<SomeClass::EnumClass> e(m, "SomeClassEnumClass");
    e.value("Foo", SomeClass::EnumClass::Foo);
    e.value("Bar", SomeClass::EnumClass::Bar);
}
```

注意，枚举值不能是 Python 中的保留关键字。因此不允许使用 `None`、`True`、`False` 等名称。如果您的枚举具有这些名称的值，请使用不同的名称注册它们。

如果枚举用作一组标志，可以使用 `Falcor::ScriptBindings::addEnumBinaryOperators(enum)` 辅助函数在枚举类型上注册二元运算符。

注册枚举时，将类名作为枚举名称的一部分。这是一种变通方法，因为 Python 不包含类名，如果不同类中存在同名的枚举类型，可能会产生名称冲突。

约定是 C++ 枚举 `MyClass::Type` 应注册为 `MyClassType`。例如：

`pybind11::enum_<MyClass::Type> e(m, "MyClassType");`

**注意：**
您可能会想将枚举与 `uint` 相互转换。不要这样做，这可能导致向后兼容性问题。将此视为快速而粗糙的解决方案，仅在 pass 初期开发时使用。

### 注册可序列化结构体

如果您想暴露可序列化为 Python 字典的数据结构体（即用于存储配置 render pass 的选项），可以使用 `Falcor::ScriptBindings::SerializableStruct` 辅助工具。假设我们有如下结构体：

```c++
struct SomeOptions
{
    float foo;
    int bar;
    std::string str;
}
```

此结构体可以使用以下方式暴露给 Python：

```c++
static void regExamplePass(pybind11::module& m)
{
    ScriptBindings::SerializableStruct<SomeOptions> someOptions(m, "SomeOptions");
    someOptions.field("foo", &SomeOptions::foo);
    someOptions.field("bar", &SomeOptions::bar);
    someOptions.field("str", &SomeOptions::str);
}
```

使用局部宏可以减少一些重复输入：

```c++
static void regExamplePass(pybind11::module& m)
{
    ScriptBindings::SerializableStruct<SomeOptions> someOptions(m, "SomeOptions");
#define field(f_) field(#f_, &SomeOptions::f_)
        someOptions.field(foo);
        someOptions.field(bar);
        someOptions.field(str);
#undef field
}
```

有关 Python 绑定的更多信息，请查阅 [pybind11 文档](https://pybind11.readthedocs.io/en/stable/)
