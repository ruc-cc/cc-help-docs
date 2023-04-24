# Python

Python是数据分析、数据挖掘和机器学习领域经常使用的一种编程语言。在计算平台平台上，有两种使用Python的方式：

1. [JupyterLab](jupyterlab.md)和[VSCode](vscode.md)
2. 在共享集群中提交作业，共享集群的介绍和使用方式详见[文档](GPU-Cluster.md)

其中，第一种方式适合对Linux不太熟悉的用户，第二种方式适合有一定Linux基础，同时计算量较大，可以同时提交多个作业，以验证不同的参数。我们建议新人可以从JupyterLab或VSCode开始上手，如果发现资源需求较大，可逐渐将工作模式迁移到第二种方式上。本文主要展示如何使用共享集群，向集群上提交Python作业任务。

## 1. Python包安装

!!! warning "Jupyter与共享集群"
    共享集群与Jupyter等交互实例是相互独立的，在Jupyter中安装的一些软件包，并不能直接在共享集群里直接使用，两边都需要单独安装。

### 1.1 conda与环境变量

!!! tip "提示"
    在计算平台上，我们强烈建议用户使用 Anaconda 来管理和使用Python和R。我们已经在计算平台的共享集群和交互实例上都安装好了 Anaconda 。`conda` 命令的使用方法可以详见我们提供的[入门教程](conda.md)。共享集群的`conda`位于`/opt/app/anaconda/bin/conda`。

使用`conda`前，先要将`conda`添加到环境变量中。

* 方法1：

如果用户经常使用`conda`管理各类环境，可以将其添加到用户环境变量，即将下面的环境变量添加到`~/.bashrc`的**最后一行**。添加后再在Terminal命令行中执行`source ~/.bashrc`，这样之后，包括登录节点和计算节点在内的所有节点都可以直接使用`conda`了。

```bash
export PATH="/opt/app/anaconda/bin:$PATH"
```
* 方法2：
  

可以使用`module`，每次使用前，将`conda`添加到环境变量：

```bash
module load anaconda3
```

这种方法只是在每次使用时有效，登录到任何一个计算节点，还需要重新执行一遍`module load anaconda3`。

### 1.2 创建环境并安装包

我们强烈建议用户使用`conda`创建属于自己版本的Python，这样有几个好处：

1. Python生态中各个包版本迭代速度很快，基于不同的环境，可以使用不同的包版本。
2. 可以为某项任务、某个工程创立单独的环境，环境之间相互隔离，方便相互切换。
3. 如果不创建单独环境，当默认环境中的包越来越多时，包与包之间会产生一些冲突，导致整个环境不可用。创建单独的环境可以避免包冲突。

比如，我们想创建Python3.8、TensorFlow 2.2的环境，环境名为`tf22`：

```bash
conda create --name tf22 python=3.8
```

相关包将安装到`/home/your-id/.conda/envs/...`下。

激活这个环境：

```bash
source activate tf22
```

在环境上安装 TensorFlow：

```bash
conda install tensorflow-gpu -n tf22
```

## 2. 在共享集群上提交作业

### 2.1 在Slurm中提交作业

对于共享集群不熟悉的用户，可以先阅读[共享集群文档](GPU-Cluster.md)。

使用Slurm提交作业，首先编写一个作业提交脚本，将其命名为：`test.sh`：

```bash
#!/bin/bash

### 将本次作业计费到导师课题组，tutor_project改为导师创建的课题组名
#SBATCH --comment=tutor_project

### 给您这个作业起个名字，方便识别不同的作业
#SBATCH --job-name=tf-example

### 指定该作业需要多少个节点
### 注意！没有使用多机并行（MPI/NCCL等），下面参数写1！不要多写，多写了也不会加速程序！
#SBATCH --nodes=1

### 队列名，目前可用的GPU队列为a100/v100/titan
#SBATCH --partition=titan

### 使用GPU数，一块GPU卡默认配置了一定数量的CPU核
### 注意！程序没有使用多卡并行优化的，下面参数写1！不要多写，多写也不会加速程序！
#SBATCH --gpus=1

### 以上参数用来申请所需资源
### 以下命令将在计算节点执行

### Anaconda
export PATH=/opt/app/anaconda/bin:$PATH

### 假设创建的conda环境名为 tf22
source activate tf22

python tf_test.py
```

然后在命令行中使用`sbatch`提交作业：

```
sbatch test.sh
```

![使用sbatch提交作业](../images/sbatch.png)

其他Slurm命令，详见[共享集群文档](GPU-Cluster.md)。

## 3. PyTorch & TensorFlow

### 3.1 PyTorch

#### CUDA & 安装

!!! warning "CUDA版本"
    目前，我们在共享集群的各个GPU节点上提供的驱动兼容CUDA 11.x 版本，也提供了默认的CUDA，CUDA路径为：`/opt/pkgs/cuda`。您可以使用当前默认版本的CUDA，也可以使用 `conda` 安装所需要的CUDA版本。

    更多CUDA安装和兼容性知识，详见[CUDA](./cuda.md)。

创建自己的环境，激活环境：

```
# 创建名为 torch1.8 的环境
conda create -n torch1.8

# 激活这个环境
source activate torch1.8
```

大家可以前往PyTorch官网提供的安装指南查看安装命令： [https://pytorch.org/get-started/locally/](https://pytorch.org/get-started/locally/) 。

以下面的安装命令为例：

```
conda install pytorch==1.8.0 cudatoolkit=11.1 -c pytorch -c conda-forge
```

其中，`pytorch==1.8.0 torchvision==0.9.0 torchaudio==0.8.0` 为 PyTorch 相关包，`cudatoolkit=11.1` 表示同时也安装 CUDA 11.1。那么，环境里既安装了 `torch` 又安装了 CUDA 11.1。

#### 多卡并行

如果需要多个节点提供计算，需要在代码中加入多机多卡的API。同时，在Slurm脚本中申请资源时，按需申请多机多卡。

### 3.2 TensorFlow

#### 安装

创建自己的环境，激活环境：

```
# 创建名为 tf27 的环境
conda create -n tf2.7

# 激活这个环境
source activate tf2.7
```

在 [https://anaconda.org/](https://anaconda.org/) 中搜索 `tensorflow-gpu`，找到安装命令，比如：

```bash
conda install -c conda-forge tensorflow-gpu==2.7.1
```

#### 多卡并行

如果需要多个节点多个GPU进行并行计算，则需要在Python代码中添加TensorFlow提供的分布式计算API。

同时，在Slurm申请资源时要根据需求申请多节点多GPU卡资源。

## 4. TensorBoard

在共享集群上可以使用TensorBoard，需要在SSH登录时进行端口转发。

* SSH登录

```bash
ssh -L 127.0.0.1:10060:127.0.0.1:10060 -p 20014 u20200002@login.cc.ruc.edu.cn
```

以上命令登录的同时，也转发了10060端口，我们下面使用这个端口来启动TensorBoard服务。

用户需要先安装TensorBoard，可以使用`conda`或者`pip`，这里不再赘述。安装好后，可以在登录节点启动：

```bash
tensorboard --port=10060 --logdir=./
```

这里使用了`--port`参数，且端口号与刚刚端口转发的端口号保持一致，建议使用一个较大的数字，避免和其他用户相冲突。

* VSCode Remote

VSCode的Remote插件在SSH登录时，会把所有端口都进行转发，启动TensorBoard后，会直接监测到端口启动，无需`ssh -L 127.0.0.1:10060:127.0.0.1:10060 -p 20014 u20200002@login.cc.ruc.edu.cn`这样逐个端口转发。

在浏览器里直接访问：http://127.0.0.1:10060/ 即可打开TensorBoard界面。