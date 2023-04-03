# 容器技术：Docker与Singularity

容器可用于软件环境和资源隔离，可执行程序与依赖库打包成一个镜像文件，启动时像启动一个普通进程一样，启动操作系统与各个依赖库。常见的容器技术为Docker，在高性能计算领域，一般使用Singularity。Singularity可兼容Docker，如果您想使用某个Docker镜像，需要先使用Singularity将Docker镜像转化为Singularity镜像格式。

容器的具体使用流程为：

1. 在某台安装有Singularity的服务器上构建（Build）镜像
2. 将镜像上传至计算平台，使用Singularity提供的命令启动计算任务

## Singularity安装

在正式使用前，需要先在**自己的服务器**上安装Singularity，目前计算平台上无专用的服务器用于安装Singularity。

从[Singularity官网](https://sylabs.io/)中选择某个版本的User Guide，找到安装方式，比如[3.5 User Guide](https://sylabs.io/guides/3.5/user-guide/)。

安装过程主要包括：

1. 安装必要的依赖
2. 安装Go
3. 下载并编译安装某个版本的Singularity

## 镜像构建（Build）

安装好Singularity后，即可使用各类镜像，例如以`docker://`开头的各类镜像。

```bash
singularity pull pytorch-20.12.sif docker://nvcr.io/nvidia/pytorch:20.12-py3
```

上面的命令将生成名为`pytorch-20.12.sif`的Singularity镜像。

## 使用容器提交计算任务

在Slurm共享集群上，修改提交作业的脚本，例如：

```bash
#!/bin/bash

### 将本次作业计费到导师课题组，tutor_project改为导师创建的课题组名
#SBATCH --comment=tutor_project

### 给您这个作业起个名字，方便识别不同的作业
#SBATCH --job-name=singularity

### 指定该作业需要多少个节点
### 注意！没有使用多机并行（MPI/NCCL等），下面参数写1！不要多写，多写了也不会加速程序！
#SBATCH --nodes=1

### 指定该作业在哪个队列上执行
### 目前可用的GPU队列有 titan/tesla
#SBATCH --partition=tesla

### 申请一块GPU卡，一块GPU卡默认配置了一定数量的CPU核
### 注意！程序没有使用多卡并行优化的，下面参数写1！不要多写，多写也不会加速程序！
#SBATCH --gpus=1 

### 以上参数用来申请所需资源
### 以下命令将在计算节点执行

### 使用singularity，执行您的作业
singularity exec --nv /path/to/pytorch-20.12.sif python test.py
```

在上面这个提交作业的脚本中，主要是将原来作业提交的命令`python test.py`修改为`singularity exec --nv /path/to/pytorch-20.12.sif python test.py`，其中`--nv`选项为使用Nvidia的GPU，更多选项请参考Singularity官方用户文档。