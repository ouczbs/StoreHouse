# 安装

## xmake

构建工具

[github](https://github.com/xmake-io/xmake/releases)

## clang

c++ 编译器

[github](https://github.com/llvm/llvm-project/releases)

# 使用

## 库

package

- 生成Package
- 添加Package

## 配置

![image-20230215085610374](E:\ouczbs\StoreHouse\职业\专业积累\工具语法\assets\image-20230215085610374.png)

强行修改 xmake.conf

通过 xmake f 设置会失败

```lua
--全局配置
xmake g --ndk=~/files/android-ndk-r10e/
--清除配置
xmake f -c
--导出配置
xmake f --export=config.txt
xmake f -m debug --xxx=y --export=config.txt
--环境变量
xmake show -l envs
```



## 语法

### 描述域

```lua
target("test")
    set_kind("binary")
    add_files("src/*.c")
    add_defines("DEBUG")
    add_syslinks("pthread")
target "test"
    set_kind "binary"
    add_files "src/*.c"
    add_defines "DEBUG"
    add_syslinks "pthread"
--xmake.lua是会被多次解析的，用于在不同阶段解析不同的配置域：比如：option(), target()等域。
target("demo", {kind = "binary", files = "src/*.c"})
```



一眼望去，其实就是个 `set_xxx`/`add_xxx`的配置集，对于新手，完全可以不把它当做lua脚本，仅仅作为普通的，但有一些基础规则的配置文件就行了。

如果因为，看着有括号，还是像脚本语言的函数调用，那我们也可以这么写（是否带括号看个人喜好）：



### 脚本域

```lua
target("test")
    set_kind("binary")
    add_files("src/*.c")
    on_load(function (target)
        if is_plat("linux", "macosx") then
            target:add("links", "pthread", "m", "dl")
        end
    end)
    after_build(function (target)
        import("core.project.config")
        local targetfile = target:targetfile()
        os.cp(targetfile, path.join(config.buildir(), path.filename(targetfile)))
        print("build %s", targetfile)
    end)
    on_load("modules.test.load")
    on_install("modules.test.install")
```



只要是类似：`on_xxx`, `after_xxx`, `before_xxx`等字样的function body内部的脚本，都属于脚本域。

在脚本域中，用户可以干任何事，xmake提供了import接口可以导入xmake内置的各种lua模块，也可以导入用户提供的lua脚本。

我们可以在脚本域实现你想实现的任意功能，甚至写个独立项目出来都是可以的。

对于一些脚本片段，不是很臃肿的话，像上面这么内置写写就足够了，如果需要实现更加复杂的脚本，不想充斥在一个xmake.lua里面，可以把脚本分离到独立的lua文件中去维护

### 配置域

`target()`, `option()`, `task()`, `package()`

### 配置顶

只要是带有`set_xxx`和`add_xxx`字样的配置，都属于配置项，一个配置域里面可以设置多个配置项

### 作用域

全局变量与局部变量同 lua

配置顶有作用范围 全局与配置域

按文件有继承配置域

### 多级配置

描述域通过includes 导入子描述

脚本域通过import导入子脚本

导入后就可以直接使用里面的所有公有接口，私有接口用`_`前缀标示，表明不会被导出，不会被外部调用到。

其中 main 为入口函数，可选，如果设置，模块 foo 可以直接被调用

```lua
--模块继承
import("xxx.xxx", {inherit = true})
```

### [项目描述设置](https://xmake.io/#/zh-cn/guide/configuration?id=项目描述设置)

- set_toolchains
  - `xmake show -l toolchains`
- set_toolset
- set_config

```lua
target("test")
    set_kind("binary")
    add_files("src/*.c")
    set_toolchains("clang", "yasm")
target("test")
    set_kind("binary")
    set_toolset("cxx", "clang")
    set_toolset("ld", "clang++")
set_config("cflags", "-DTEST")
set_config("sdk", "/home/xxx/tooksdk")
set_config("cc", "gcc")
set_config("ld", "g++")
```

辅助接口

```lua
if is_plat("myplat") then
    add_defines("TEST")
end
xmake f -p myplat --sdk=/usr/local/arm-xxx-gcc/
xmake
```



```lua
set_project("vulkan")

set_arch("x64")
set_warnings("all")
set_languages("c++20")
-- 设置语法编译器，若gcc、clang不是全局变量则无法编译成功
set_toolchains("clang")

add_rules("mode.debug","mode.releasedbg", "mode.release", "mode.minsizerel")
add_requires("protobuf-c")
add_requires("vulkansdk", "glfw", "glm")

target("library")
    set_kind("static")
    add_files("src/library/*.c")

target("main")
    set_default(true)
    set_kind("binary")
    add_files("src/main.cpp")
    add_packages("protobuf-c")
    add_rules("protobuf.c")
    add_packages("vulkansdk", "glfw", "glm")
	add_deps("library")
```

# VS

```lua
xmake project -k vsxmake -a x64
```

# VS CODE

https://zhuanlan.zhihu.com/p/398790625

[万字长文]Visual Studio Code 配置 C/C++ 开发环境的最佳实践(VSCode + Clangd + XMake)

# Package

![image-20230213191419392](E:\ouczbs\StoreHouse\职业\专业积累\工具语法\assets\image-20230213191419392.png)

# QA

## xmake f 

强行修改 `.xmake\windows\xmake.conf`

`xmake f -a=x64 -p=windows`

xmake f -cvD



## add_requires

```c++
pulling repository(build-artifacts): https://github.com/xmake-mirror/build-artifacts.git to C:\Users\Administrator\AppData\Local\.xmake\repositories\build-artifacts ..
git -c core.fsmonitor=false pull origin main
fatal: not a git repository (or any of the parent directories): .git
error: execv(git -c core.fsmonitor=false pull origin main) failed(128)
```



手动 git clone  https://github.com/xmake-mirror/build-artifacts.git

## can't build

```lua
xmake -vD verbose Diagnosis 
```

## curl ssl problem

 由cartalyst / stripe使用的Guzzle将执行以下操作来查找适当的证书归档，以检查服务器证书是否符合以下条件： 

1.  检查你的php.ini文件是否设置了`openssl.cafile` 。 
2.  检查`curl.cainfo`是否在您的php.ini文件中设置。 
3.  检查是否存在`/etc/pki/tls/certs/ca-bundle.crt` Hat，CentOS，Fedora;由ca-certificates包提供） 
4.  检查`/etc/ssl/certs/ca-certificates.crt`存在（Ubuntu，Debian;由ca-certificates包提供） 
5.  检查`/usr/local/share/certs/ca-root-nss.crt`存在（FreeBSD;由ca_root_nss包提供） 
6.  检查`/usr/local/etc/openssl/cert.pem` X;由homebrew提供） 
7.  检查是否存在`C:\windows\system32\curl-ca-bundle.crt` （Windows） 
8.  检查是否存在`C:\windows\curl-ca-bundle.crt` （Windows） 

```lua
https://curl.haxx.se/docs/caextract.html
curl-ca-bundle.crt
```

## curl 慢

连 strong vpn 就行



