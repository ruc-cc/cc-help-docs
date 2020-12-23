# 使用Modules和Spack管理集群软件

[Environment Modules](https://modules.readthedocs.io/en/latest/)和[Spack](https://spack.readthedocs.io/en/latest/)是大型超级计算集群中经常使用的两种包管理软件。

## 环境变量

开始介绍前，我们先了解一些基础知识。Linux操作系统中，软件被安装到某个路径下，比如`/usr/local/bin/python3`。如果想使用某个软件，或者使用这个软件的绝对路径；或者将软件的绝对路径添加到环境变量里，下次使用无需使用绝对路径。Linux有几个重要的环境变量：

| 环境变量        | 功能                 | 案例                                                         |
| --------------- | -------------------- | ------------------------------------------------------------ |
| PATH            | 指定可执行文件的路径 | `xxxx/bin`下的可执行文件，比如`python`、`gcc`等。            |
| LD_LIBRARY_PATH | 指定依赖库的路径     | `xxxx/lib`下的依赖库，比如动态链接库`.so`文件，静态链接库`.a`文件等。 |

Linux操作系统的某个发行版一般会提供一些默认的软件，比如`gcc`等。使用操作系统自带的包管理工具，比如`apt-get`或者`yum`安装的软件，这些软件由包管理工具编译和打包，软件会安装到操作系统的特定文件夹下，这些特定文件夹一般已经加入到了环境变量中，可以直接使用。这些软件是面向整个系统所有用户生效的。

在我们的共享集群中，普通用户没有`root`权限，无法使用`apt-get`或`yum`包管理工具安装特定的软件，常用的两种实践是：

1. 将所需软件源代码下载到个人目录，使用编译工具编译，将编译好的软件路径添加到用户个人的环境变量中，比如`~/.bash_profile`或`~/.bashrc`中。比如，我们想将Anaconda放到环境变量里，可以在`~/.bashrc`中添加`export PATH="/opt/app/anaconda3/bin:$PATH"`，以后就可以直接使用。
2. 集群管理员预先安装一些软件，使用针对集群的包管理软件`module`或`spack`来管理不同版本的软件，用户需要使用`module`或`spack`切换到所需的软件环境中。

下面重点介绍第2种方式。

## module

### 快速入门

`module`工具是超级计算集群经常使用的包管理软件，全称为Environment Modules，相关详细文档参考[链接](https://modules.readthedocs.io/en/latest/)。其核心思想为：

* 管理员将某些软件安装到某个路径，将相关配置添加到`module`中。
* 用户使用`module`提供的命令工具，加载所需的软件环境，即：在当前用户登录的Session中，`module`自动将所需软件的路径添加到`PATH`、`LD_LIBRARY_PATH`等环境中。

用户可以先查看共享集群已经安装了哪些软件：

```bash
$ module avail
```

目前已经基于不同版本的编译器安装了众多软件，`module avail`显示的每个条目为某个具体的软件环境，这里以使用GCC 9.3.0为例，加载这个软件环境：

```bash
$ module load gcc-9.3.0-gcc-4.8.5-oxjixak
```

这时，当前Session将使用GCC 9.3.0。使用`which gcc`可以查看`gcc`可执行文件的路径。

```bash
$ which gcc
/opt/app/spack/opt/spack/linux-centos7-haswell/gcc-4.8.5/gcc-9.3.0-oxjixak2sy5mudydbjatzvj4bpveh5jl/bin/gcc
```

!!! warning "module的生命周期"
    如果使用`module`加载软件，`module`只管理当前登录Session，Session结束（比如退出SSH登录）再次登录时，需要重新使用`module`加载软件。或者说，`module`只在当前登录Session中将所需软件添加到`PATH`等环境变量，退出当前登录后，`PATH`等环境变量恢复原样。使用Slurm提交作业与之类似，需要在Slurm作业提交脚本中的计算部分前使用`module`加载软件。

常用的`module`命令如下：

| 命令            | 功能                       |
| --------------- | -------------------------- |
| `module avail`  | 显示已经安装的软件环境     |
| `module load`   | 加载相应的软件环境         |
| `module unload` | 退出相应的软件环境         |
| `module list`   | 列出已经加载的软件环境     |
| `module purge`  | 退出所有已经加载的软件环境 |
| `module help`   | 显示帮助信息               |

### 软件命名规则

在我们的共享集群中，绝大多数软件是使用`spack`安装的。`spack`在安装软件时，命名遵循了一些规则。

`spack`安装的软件命名有三部分：软件名+版本号-编译所使用的编译器+版本号-唯一标识符。例如，`gcc-9.3.0-gcc-4.8.5-oxjixak`为GCC 9.3.0，它由GCC 4.8.5编译而生成。

不同的`---`将不同的软件分成了不同的组，其中`/opt/app/spack`为使用`spack`安装的软件，每组所使用的编译器不同。

```
--- /opt/app/spack/share/spack/modules/linux-centos7-haswell ---
...
```

上面均为基于CentOS GCC 4.8.5编译的软件。更高版本的GCC也是基于GCC 4.8.5编译的，比如`gcc-9.3.0-gcc-4.8.5-oxjixak`是基于GCC 4.8.5编译的。

```
--- /opt/app/spack/share/spack/modules/linux-centos7-skylake_avx512 ---
...
```

上面均为基于CentOS GCC 6.x编译的软件。比如，`cuda-10.1.243-gcc-6.5.0-lg73xtg`是基于GCC 6.5.0编译的。

```
--- /opt/app/spack/share/spack/modules/linux-centos7-cascadelake ---
```

上面均为基于CentOS GCC 9.x编译的软件。

```
--- /opt/app/modulefiles ---
...
```

上面均为单独安装的软件，即未使用`spack`编译安装的软件。

## spack

现代软件越来越复杂，软件本身有不同的版本，同时又依赖很多其他软件和包。有人提出了使用更方便的面向集群的包管理工具`spack`，相关详细文档请参考[链接](https://spack.readthedocs.io/en/latest/)。

`spack`团队帮我们准备好绝大多数常用软件以及其依赖，将这些依赖整理成了有向无环图，并将这些软件放置在AWS上。安装时直接指定所需软件版本，`spack`帮我们从AWS上下载编译好的软件到某个路径，软件安装好后，相应的配置也会添加到`module`系统中。

我们可以查看系统已经安装了哪些软件：

```bash
$ spack find

-- linux-centos7-cascadelake / gcc@9.3.0 ------------------------
autoconf@2.69                cuda@10.2.89       gettext@0.20.2   ...
```

不同的包基于不同的GCC编译得到，上面罗列的包基于GCC 9.3.0。

也可以加载某个软件：

```bash
$ spack load cuda@10.2.89%gcc@9.3.0
```

`spack`中，`@`表示使用哪个版本，`%`表示某个软件依赖哪个编译器。

如果有需要安装的软件，可以先使用`spack list | grep 'software_name'`查看，然后给<hpc@ruc.edu.cn>发邮件，我们会帮你安装。

