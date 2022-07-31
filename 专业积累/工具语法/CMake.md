

### CMake

一个跨平台的自动构建系统，使用一个 CMakeLists.txt 文件来描述构建过程（等同于 Linux 系统下编译构建 C/C++ 时使用的 Makefile 的作用），通常在编译一个多文件的工程时使用这样的工具。

- **安装：**

  直接下载官方 [cmake-3.13.0-rc3-win64-x64.zip](https://cmake.org/files/v3.13/cmake-3.13.0-rc3-win64-x64.zip) 包，解压即可，无需安装。

- **配置：**

  参考 MinGW 的配置方式，将解压后目录中的 bin 目录配置到系统 的 Path 参数中，通过命令行验证是否配置成功：

### CMakeLists.txt

关于 `CMakeLists.txt` 的语法还是需要再补充一下的，可以参考：[cmake-tutorial](https://cmake.org/cmake-tutorial/)

当然，在使用过程中会发现很多很微妙的地方，例如： `link_directories` 必须在 `add_executable` 之前使用，这样 `target_link_libraries` 才能从 `link_directories` 中指定的目录中去搜索库文件。

### GCC 、Make 和 CMake

这里有必要梳理一下 `gcc` 、`make` 以及 `cmake` 的关系：

- `gcc`：

  即 GNU Compiler Collection（GNU 编译器套件），也可以理解为编译器，可用于编译很多种编程语言（如：C、C++、Objective-C、Java 等）；

- `make` ：

  可以看作一个智能的批处理工具，本身没有编译和链接功能，而是通过调用 `makefile` 文件中用户指定的命令来进行编译和链接的；

- `cmake` ：

  可以根据 `CMakeLists.txt` 文件，跨平台来生成对应平台的 makefile 文件。

综上所述：

- gcc 是用于编译和链接和工具，编译少量文件可以直接使用 gcc 命令完成，但当源文件很多，用 gcc 命令去逐个编译则是很混乱且工作量巨大。
- 因此，需要借助 make 来来管理整个编译过程，make 安装 makefile 中的命令进行编译和链接，而 makefile 命令中就包含了调用 gcc 去编译某个源文件的命令。
- 而当工程规模非常大时，且需要跨平台时，手写 makefile 也开始变得麻烦，这是可以借助 cmake 来生成 makefile 。