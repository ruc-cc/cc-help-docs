# 编译器

在[module/spack](./module-spack.md)中我们提到有两种使用软件的方式：1.用户自己编译，自己使用，2.管理员编译，整个共享集群均可使用。本页将针对第1种需求，介绍如何使用特定版本的编译器完成编译工作。

在Slurm集群中，编译器被安装在某个共享的目录下，用户使用`module`或`spack`加载编译器环境变量，编译成二进制文件，在运行时，将环境变量添加到作业脚本中。

## 编译与运行

先使用`module`加载编译器的环境变量。

对于特别小的编译任务（小于半小时），可在登录节点（workstation）编译。

!!! warning "编译超过半小时，请申请一个就算节点，在计算节点编译！"
    登录节点(workstation)是所有用户登录的公共节点，应尽量避免占用登录节点大量资源。当某个软件需要超过半小时以上的编译时间，请用`salloc`申请一个计算节点，在计算节点编译。登录节点设置了限制时间，超过半小时的任何任务可能被杀死。

成功编译后，运行这个任务需要使用Slurm调度系统中启动一个作业，记得在Slurm脚本中，添加编译器环境变量。

## Intel编译器

本集群的CPU绝大多数为Intel，2020年之前的版本为Parallel Studio，自2021年开始，Intel编译套件开源，被称为oneAPI。Intel编译器安装在：`/opt/app/intel`。

用户可以使用`module`来加载Intel环境变量：

```bash
module load intel/2021
```

检查环境变量中的编译器：

```bash
which icc    # /opt/app/intel/2021/compiler/2021.3.0/linux/bin/intel64/icc
which ifort  # /opt/app/intel/2021/compiler/2021.3.0/linux/bin/intel64/ifort
which mpicc  # /opt/app/intel/2021/mpi/2021.3.0/bin/mpicc
which mpirun # /opt/app/intel/2021/mpi/2021.3.0/bin/mpirun
```

或者使用`source`来加载Intel环境变量：

```bash
source /opt/app/intel/2021/setvars.sh
```

以编译好的软件`vasp_std`为例，运行时，记得在Slurm作业脚本中添加编译器的环境变量：

```bash
#!/bin/bash
#SBATCH --comment=tutor_project
#SBATCH --nodes=1
#SBATCH --job-name=intel
#SBATCH --ntasks=4
#SBATCH --partition=cpu

module load intel/2021
mpirun /opt/app/vasp/5.4.4/vasp_std
```
