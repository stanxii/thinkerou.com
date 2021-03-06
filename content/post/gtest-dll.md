---
title: 使用 gtest 给 DLL 工程编写单元测试
categories: [
  "gtest"
]
tags: [
  "gtest"
]
date: 2015-07-12
image: "speakers.jpg"
---

## 背景说明
  
任何时候给你的代码增加单元测试（或接口测试）都不晚，对于 C/C++ 使用比较广泛的单元测试框架应该非 [googletest](https://code.google.com/p/googletest/) 莫属了。

通常在工程代码中，使用 gtest 比较广泛的方式是**以 lib 的形式**来做单元测试，这种方式使用也比较简单直接，本文就不再赘述。

但是，对于大部分工程代码而言，并不是只有 EXE 文件，通常这些 EXE 文件都会依赖至少一个 DLL 文件，同时这些 DLL 文件并不会导出所有函数或类，而仅导出极少的东西给外部使用。（注：本文仅讨论 gtest 在 windows 上的应用）

那么，问题来了，该如何使用 gtest 对 DLL 里没有导出的函数或类做单元测试呢？


## 解决方案

做为[示例](https://github.com/thinkerou/gtest-dll-test)，假设整个 Solution 包含如下三个 project：

  * coreTest 工程：一个被 mainTest 工程应用的 DLL 库；
 
  * mainTest 工程：整个 Solution 的 main 入口，调用 coreTest.dll 的 EXE 文件； 

  * unitTest 工程：将 gtest 包装成 DLL 的工程。

详细的代码示例及工程属性设置请阅读完整的[示例代码](https://github.com/thinkerou/gtest-dll-test)。

为了使用 gtest 对 DLL 里没有导出的函数或类进行单元测试，或许会有如下两种方法：

* 新建一个 EXE 工程，然后将所有被测文件包含进来，即可编写对应文件的 unittest，使用 gtest 的 lib 方式；

* 将 gtest 包装成一个 DLL，然后直接在 DLL 工程里编写对应文件的 unittest。

第一种方式的问题就是容易出现各种文件路径不对的问题，甚至有的时候需要修改产品代码的头文件；

第二种方式就没有那么多问题了，但是需要注意的是，**对工程属性的设置一定要正确**，下文详细介绍。


## 重要说明

在编写 gtest DLL 和使用 gtest DLL 时需要设置工程属性，特别强调如下：

* unitTest 工程需要添加 preprocessor 参数： **GTEST\_CREATE\_SHARED\_LIBRARY**

* 在 unitTest DLL 工程里导出的 gtest 的 main 函数里需要**显示 LoadLibrary(L"coreTest.dll")**，其他 DLL 类似

* 在使用 unitTest DLL 的工程（DLL 和 EXE）里需要添加 preprocessor 参数：**GTEST\_LINKED\_AS\_SHARED\_LIBRARY**

* 在每个 unittest 文件里需要 **#pragma comment(lib, "unitTest.lib")**

**总结：**在工程中以 gtest DLL 的形式来编写单元测试就是这样的，重要的也容易出错的地方就是工程属性的设置。


## 参考资料

> [gtest 官网](https://code.google.com/p/googletest/)

> [示例代码](https://github.com/thinkerou/gtest-dll-test)

> [gtest README](https://code.google.com/p/googletest/source/browse/trunk/README?r=638)
