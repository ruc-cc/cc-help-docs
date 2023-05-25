# MPI

多机并行非常依赖MPI。MPI是一个标准，有很多不同的实现，最好用的目前有两个：Intel MPI和OpenMPI。

需要使用MPI但又不熟悉的，请先阅读这些教程：

* [美国LLNL国家实验室教程](https://hpc-tutorials.llnl.gov/mpi/)
* [MPI Tutorial](https://mpitutorial.com/tutorials/)
* [两小时入门MPI知乎教程](https://zhuanlan.zhihu.com/p/399150417)

## 1. Intel MPI

Intel MPI 库是一个实现开源 MPI 规范的消息传递库。使用Intel MPI在基于Intel处理器集群上表现更好。通过 `module avail` 可以看到当前已经安装的软件，找到 `intel-oneapi-mpi` 字样的软件。

```
module load intel-oneapi-mpi/2021.9.0
```

Intel MPI 适配了 SLURM ，它可以探测到 SLURM 分配了多少节点，节点的 hostname 是什么，所以通常情况下，可以直接使用 `mpirun` 提交作业，不需要设置 `-hosts`，主要关注启动多少个进程，使用 `-np` 设置启动多少进程。例如，申请了2个节点：

```
#!/bin/bash

#SBATCH --job-name=mpihello
#SBATCH --partition=cpu64c
#SBATCH --nodes=2
### 其他参数请根据自己情况设置

ulimit -s unlimited
ulimit -l unlimited

module load intel-oneapi-mpi/2021.9.0

mpirun -np 2 ./mpihello
```

## 2. OpenMPI

OpenMPI是一个免费的、开源的MPI实现，由开源社区开发维护，支持大多数类型的HPC平台，并具有很高的性能。

使用 `module avail` 查看带有 `openmpi` 字样的软件，并加载：

```bash
module load openmpi/4.1.5-gcc-13.1.0
```

## 3. mpi4py

`mpi4py` 是 MPI 的 Python 接口。

#### 3.1 pip 安装

安装之前请先用上面的方式选择一个 MPI 实现，先进行 `module load` 再使用 `pip install mpi4py` 安装。

#### 3.2 conda 安装

`mpi4py` [官方文档](https://mpi4py.readthedocs.io/en/latest/install.html#using-conda)里提供了 `conda` 安装的方式，`conda` 安装 `mpi4py` 的同时，也在环境里安装了所需的 MPI。

