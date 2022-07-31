# Docker

就是容器引擎中的佼佼者。

开发者们在笔记本电脑上写完一个软件后，可以将它转移到云服务上运行而无需做出更改；无论是自己的服务器、数据中心还是Google、微软、阿里云的云计算服务器，开发人员都可以按自己的想法在任何基础设施之间转移自己的软件。

## 基本概念

### 镜像：一个特殊的文件系统

Docker 镜像是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数

Docker 设计时，就充分利用 Union FS 的技术，将其设计为分层存储的架构。 镜像实际是由多层文件系统联合组成。

### 容器：镜像运行时的实体

镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的类和实例一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等 。

容器的实质是进程，但与直接在宿主执行的进程不同，容器进程运行于属于自己的独立的命名空间。前面讲过镜像使用的是分层存储，容器也是如此。

容器存储层的生存周期和容器一样，容器消亡时，容器存储层也随之消亡。因此，任何保存于容器存储层的信息都会随容器删除而丢失。

按照 Docker 最佳实践的要求，容器不应该向其存储层内写入任何数据 ，容器存储层要保持无状态化。

所有的文件写入操作，都应该使用数据卷（Volume）、或者绑定宿主目录，在这些位置的读写会跳过容器存储层，直接对宿主（或网络存储）发生读写，其性能和稳定性更高。

数据卷的生存周期独立于容器，容器消亡，数据卷不会消亡。因此， 使用数据卷后，容器可以随意删除、重新 run，数据却不会丢失。

### 仓库：集中存放镜像文件的地方

镜像构建完成后，可以很容易的在当前宿主上运行，但是， 如果需要在其他服务器上使用这个镜像，我们就需要一个集中的存储、分发镜像的服务，Docker Registry 就是这样的服务。

一个 Docker Registry 中可以包含多个仓库（Repository）；每个仓库可以包含多个标签（Tag）；每个标签对应一个镜像。

所以说，镜像仓库是 Docker 用来集中存放镜像文件的地方，类似于我们之前常用的代码仓库。

通常，一个仓库会包含同一个软件不同版本的镜像，而标签就常用于对应该软件的各个版本 。

我们可以通过<仓库名>:<标签>的格式来指定具体是这个软件哪个版本的镜像。如果不给出标签，将以 latest 作为默认标签。

## 安装

- 创建硬链接
- 以管理员身份打开命令行

```shell
mklink /j "C:\Program Files\Docker" "E:\Program Files\Docker"

mklink /J "C:\Users\NING MEI\AppData\Roaming\Docker Desktop" "E:\Program Files\Roaming\Docker Desktop"
mklink /J "C:\Users\NING MEI\AppData\Roaming\Docker" "E:\Program Files\Roaming\Docker"
mklink /J "C:\Users\NING MEI\AppData\Roaming\DockerToolbox" "E:\Program Files\Roaming\DockerToolbox"
mklink /J "C:\Users\NING MEI\AppData\Local\Docker" "E:\Program Files\Local\Docker"
```
### 问题

# WSL 2 installation is incomplete

![image-20220630141156669](C:\Users\NING MEI\AppData\Roaming\Typora\typora-user-images\image-20220630141156669.png)

下载一个WSL更新包

https://link.zhihu.com/?target=https%3A//wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi

## 卸载

