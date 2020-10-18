# 在共享集群上提交MATLAB作业

## 使用调度软件

我们已经在共享集群上安装好了MATLAB，只要用户自己上传好个人数据和程序，即可向共享集群提交作业。在共享集群上，我们使用Slurm作为调度软件，不了解共享集群和调度软件的可以先阅读我们提供的[共享集群快速入门文档](./GPU-Cluster.md)。

例如，我们在`/home/your-id/`的HOME目录下准备好相关的MATLAB代码，名为`test.m`，这段程序创建了一个矩阵。准备一个作业提交脚本 `submit_matlab.sh`，<font color=red >将`submit_matlab.sh`脚本跟你的MATLAB放在同一个文件夹下</font>。其中 `-nodisplay` 表示不使用图形化界面， `-nosplash` 表示启动matlab时不显示闪屏版权信息：

```bash
#!/bin/bash

### 给你这个作业起个名字，方便识别不同的作业
#SBATCH --job-name=matlab-example

### 指定该作业需要多少个节点
#SBATCH --nodes=1

### 指定该作业需要多少个CPU
#SBATCH --ntasks=64

### 指定该作业在哪个队列上执行
### 目前可用的CPU队列有 cpu/fat
### cpu队列有64核，fat队列有128核
#SBATCH --partition=cpu

### 使用何种版本的MATLAB
module load matlab/2020a

### 执行MATLAB程序
### -nodisplay 表示不使用图形化界面， -nosplash 表示启动matlab时不显示闪屏版权信息
matlab -nodisplay -nosplash < test.m
```

接着，我们就可以在Terminal命令行中提交这个作业：`sbatch submit_matlab.sh`。

![在命令行中提交作业](../imaegs/../images/sbatch.png)

接着，命令行会提示：`Submitted batch job xxxx`，表示我们提交了一个编号为`xxxx`的作业。

然后可以使用`squeue`查看所有人的作业。查看自己提交的作业信息：

```bash
$ squeue -u `whoami`
```

等程序执行完，一些输出会打印到相同目录的`slurm-xxxx.out`的文件中。

## 在交互式MATLAB与共享集群间拷贝数据

用户可能一开始使用交互式的MATLAB，然后逐渐迁移到共享集群上。在交互式MATLAB中，计算云平台会自动将共享集群的Home目录挂载到`/group_homes`下。 用户可以在交互式的MATLAB里把脚本和数据直接复制或移动到共享集群模式的Home目录中。

例如，在一个交互MATLAB实例里，鼠标点击右键，启动Terminal。下面的命令将交互式MATLAB中的HOME目录数据的拷贝到共享集群中：`cp ~/files-to-copy /group_homes/public_cluster/home/u20200002/`。注意，将里面的学工号改为自己的学工号。