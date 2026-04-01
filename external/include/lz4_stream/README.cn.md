lz4_stream - 使用 LZ4 (解)压缩的 C++ 流
===================================================

lz4_stream 是一个简单的封装器，使用 C++ 流通过 [LZ4 compression library] 压缩和解压缩数据

使用方法
-----

查看 lz4\_compress.cpp 和 lz4\_decompress.cpp 示例命令行程序，了解如何使用此流库进行压缩和解压缩。

构建
--------

```
mkdir build
cd build
cmake ..
make
```

依赖要求
------------

使用此库需要 [LZ4 compression library]。

构建状态
------------

Ubuntu 和 OSX (GCC/Clang):

[![Build Status](https://travis-ci.org/laudrup/lz4_stream.png)](https://travis-ci.org/laudrup/lz4_stream)

Windows (MS C++):

[![Build status](https://ci.appveyor.com/api/projects/status/xrp8bjf9217broom?svg=true)](https://ci.appveyor.com/project/laudrup/lz4-stream)

代码覆盖率 (codecov.io):

[![codecov](https://codecov.io/gh/laudrup/lz4_stream/branch/master/graph/badge.svg)](https://codecov.io/gh/laudrup/lz4_stream)

许可证
-------

标准 BSD 3-Clause License，与 LZ4 库使用相同的许可证。

[LZ4 compression library]: https://github.com/lz4/lz4
[cmake]: http://cmake.org
[Google Test Framework]: https://github.com/google/googletest
