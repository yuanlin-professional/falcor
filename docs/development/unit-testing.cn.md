### [Index](../index.md) | [Development](./index.md) | 单元测试

--------

# 单元测试

Falcor 拥有自定义的单元测试系统。单元测试在 `Tools/FalcorTest` 项目中实现。

运行 `FalcorTest.exe` 时，你应该会看到类似以下的输出：

```
Running 5 tests
  unittest.cpp/TestCPUTest (CPU)                              : PASSED (0 ms)
  unittest.cpp/TestGPUTest (GPU)                              : PASSED (22 ms)
  shadingutilstests.cpp/RadicalInverse (GPU)                  : PASSED (65 ms)
  shadingutilstests.cpp/Random (GPU)                          : PASSED (1111 ms)
  shadingutilstests.cpp/SphericalCoordinates (GPU)            : PASSED (571 ms)
```

返回码为 `0` 表示测试通过，正整数表示失败的测试数量。

## 运行单元测试

### 通过脚本运行

使用适当的设置运行批处理文件 `tests/run_unit_tests.bat`。

```
$ ./run_unit_tests.bat --help
usage: run_unit_tests.py [-h] [--environment ENVIRONMENT] [--config CONFIG]
                         [--list-configs]

Frontend for running unit tests. This script helps to run the falcor unit
tests for different build configurations.

optional arguments:
  -h, --help            Show this help message and exit
  --environment ENVIRONMENT
                        Environment (default: environment/default.json)
  --config CONFIG       Build configuration (default: windows-ninja-msvc-
                        Release)
  --list-configs        List available build configurations

Additional arguments consumed by FalcorTest.exe:

  FalcorTest {OPTIONS}

    Falcor unit tests.

  OPTIONS:

      -h, --help                        Display this help menu.
      -c[all,cpu,gpu],
      --category=[all,cpu,gpu]          Test categories to run (default: all).
      -d[d3d12|vulkan],
      --device-type=[d3d12|vulkan]      Graphics device type.
      --list-gpus                       List available GPUs
      --gpu=[index]                     Select specific GPU to use
      -f[filter], --filter=[filter]     Regular expression for filtering tests
                                        to run.
      -x[path], --xml-report=[path]     XML report output file.
      -r[N], --repeat=[N]              Number of times to repeat the test.
      --enable-debug-layer              Enable debug layer (enabled by default
                                        in Debug build).
```

### 通过 Visual Studio 或命令行运行

运行可执行文件 `build/<preset name>/bin/[Debug|Release]/FalcorTest.exe`

```
  FalcorTest {OPTIONS}

    Falcor unit tests.

  OPTIONS:

      -h, --help                        Display this help menu.
      -c[all,cpu,gpu],
      --category=[all,cpu,gpu]          Test categories to run (default: all).
      -d[d3d12|vulkan],
      --device-type=[d3d12|vulkan]      Graphics device type.
      --list-gpus                       List available GPUs
      --gpu=[index]                     Select specific GPU to use
      -f[filter], --filter=[filter]     Regular expression for filtering tests
                                        to run.
      -x[path], --xml-report=[path]     XML report output file.
      -r[N], --repeat=[N]              Number of times to repeat the test.
      --enable-debug-layer              Enable debug layer (enabled by default
                                        in Debug build).
```

## 添加新的单元测试

要添加新的测试，可以编辑 `Source/Tools/FalcorTest/Tests/` 中合适的 `.cpp` 文件，或者创建一个新的 `.cpp` 文件并添加到该目录中。将新创建的文件添加到 `FalcorTest` 项目中（匹配目录结构）。

注意，单元测试代码应放在与被测试实现分离的 `.cpp` 文件中。测试用的 shader 代码应放置在测试 `.cpp` 文件*旁边*，并在项目中使用 `ShaderSource` 类型以确保 shader 文件在构建时被复制到输出目录。

确保你的 `.cpp` 测试文件中包含 `#include "UnitTest.h"`，然后就可以开始编写测试了。下面我们将分别介绍 CPU 和 GPU 单元测试的简单示例。

### CPU 测试

作为第一个示例，让我们编写一个简单的 CPU 端 `std::sqrt` 测试：

```c++
CPU_TEST(Sqrt)
{
    for (int i = 0; i < 10; ++i)
    {
        EXPECT_EQ(i, std::sqrt(i * i));
    }
}
```

`CPU_TEST` 宏负责处理向测试系统注册测试和生成相应函数签名的细节；有了它，只需添加左大括号即可开始编写代码。

`EXPECT_EQ` 宏同样定义在 `UnitTest.h` 中；它接受两个值，如果不相等则测试失败（类似地，`EXPECT_NE` 用于测试不等性，`EXPECT_{GE,GT,LE,LT}` 覆盖其余的排序关系）。最后还有 `EXPECT()`，它接受一个布尔值。

通常，最好使用 `EXPECT_*` 系列宏而非 `EXPECT`。失败时，它们会在测试输出中包含两个值。如果上述测试中 `std::sqrt(2 * 2)` 返回了 `3`，`EXPECT_EQ` 会给出如下输出：

```
Test failed: i == std::sqrt(i * i) (2 vs. 3)
```

### GPU 测试

作为第二个示例，让我们测试 GPU 上的乘法运算是否正确。

首先，编写一个简短的 compute shader，可以命名为 `Square.cs.slang`。使用 `RWStructuredBuffer` 返回结果，待乘的值通过常量缓冲区提供：

```hlsl
RWStructuredBuffer<float> result;
cbuffer Inputs { float value; };

[numthreads(1, 1, 1)]
void main()
{
    result[0] = value * value;
}
```

接下来，在 C++ 文件中添加以下代码来定义测试。

```c++
GPU_TEST(Square)
{
    ctx.createProgram("Square.slang.hlsl");
    ctx.allocateStructuredBuffer("result", 1);
    const float value = 3.f;
    ctx["Inputs"]["value"] = value;
    ctx.runProgram();

    const float *result = ctx.mapBuffer<const float>("result");
    EXPECT_EQ(result[0], value * value);
    ctx.unmapBuffer("result");
}
```

在 `GPU_TEST` 函数中，可以通过名为 `ctx` 的参数访问 `GPUUnitTestContext` 实例。`GPUUnitTestContext` 提供了多种实用方法，使得运行 GPU 端 compute 程序、分配缓冲区、设置参数和检查结果只需极少的代码。

## 输出

可以通过 `operator<<` 向所有 `EXPECT*` 宏添加额外输出，类似于 C++ 的 `std::ostream`。这些额外输出仅在测试失败时打印。因此，如果我们将 `EXPECT_EQ` 写成这样：

```c++
EXPECT_EQ(result[0], value * value) << "value = " << value;
```

那么失败时会看到：

```
Test failed: result[0] == value * value (4 vs. 9) value = 3
```

这些额外信息有助于理解出错的原因。

## 跳过测试

可以通过将 `CPU_TEST(SomeTest)` 改为 `CPU_TEST(SomeTest, "Skipped due to ...")` 来临时跳过有问题的测试。运行测试时会打印该消息，测试将以 `SKIPPED` 状态结束，这不被视为失败。同样的原则也适用于 `GPU_TEST`。
