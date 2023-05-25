# 编译器

在[module/spack](./module-spack.md)中我们提到有两种使用软件的方式：1.用户自己编译，自己使用，2.管理员编译，整个共享集群均可使用。本页将针对第1种需求，介绍如何使用特定版本的编译器完成编译工作。

在SLURM集群中，编译器被安装在某个共享的目录下，用户使用`module`或`spack`加载编译器环境变量，编译成二进制文件，在运行时，将环境变量添加到作业脚本中。

## 1. 编译与运行

先使用`module`加载编译器的环境变量。

对于特别小的编译任务（小于半小时），可在登录节点（workstation）编译。

!!! warning "编译超过半小时，请申请一个计算节点，在计算节点编译！"
    登录节点(workstation)是所有用户登录的公共节点，应尽量避免占用登录节点大量资源。当某个软件需要超过半小时以上的编译时间，请用`salloc`申请一个计算节点，在计算节点编译。登录节点设置了限制时间，超过半小时的任何任务可能被杀死。

成功编译后，运行这个任务需要使用SLURM调度系统中启动一个作业，记得在SLURM脚本中，添加编译器环境变量。

## 2. Intel oneAPI

本集群的CPU均为Intel，2020年之前的版本为需要付费的Parallel Studio，自2021年开始，Intel编译套件开源，被称为oneAPI。oneAPI包含了编译器、MKL（Math Kernel Library）、MPI和调优工具等一系列内容。

用户可以使用`module`来加载Intel环境变量：

```bash
module load intel-oneapi-compilers/2023.1.0
```

检查环境变量中的编译器：

```bash
which icc    
which ifort
```

其他编译套间均以 `intel-oneapi` 为前缀，例如 `intel-oneapi-mkl`。

## 3. GCC

GCC（GNU Compiler Collection，GNU编译器套件），多个编译器的集合，支持包括C、C++、Fortran、Ada、Object-C等。

操作系统默认的 GCC 为 4.8.5，此外我们通过 `module` 和 `spack` 安装了多种不同版本的 GCC，通过 `module avail` 可以看到。

例如：

```
module load gcc/9.5.0
```

```
which gcc
gcc --version
```


