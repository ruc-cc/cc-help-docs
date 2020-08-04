# TensorFlow

TensorFlow 是一个端到端开源机器学习平台。它拥有一个全面而灵活的生态系统，其中包含各种工具、库和社区资源，可助力研究人员推动先进机器学习技术的发展，并使开发者能够轻松地构建和部署由机器学习提供支持的应用。

!!! tip "提示"
    在计算云上，我们强烈建议用户使用 Anaconda 来管理和使用Python和R。我们已经在计算云的共享集群和交互实例上都安装好了 Anaconda 。`conda` 命令的使用方法可以详见我们提供的[入门教程](conda.md)。

## 安装

!!! warning "Jupyter与共享集群"
    共享集群与Jupyter等交互实例是相互独立的，在Jupyter中安装的一些软件包，并不能直接在共享集群里直接使用，两边都需要单独安装。

### JupyterLab

在JupyterLab中，我们已经安装好了 `conda`，可以直接在Terminal中使用，其位置位于：`/opt/conda/bin/conda`。

### 共享集群

在共享集群上，我们建议使用`conda`来创建和管理R环境，`conda` 命令的使用方法可以详见我们提供的[conda入门教程](conda.md)。共享集群的`conda`位于`/opt/app/anaconda3/bin/conda`。

使用`conda`前，先要将`conda`添加到环境变量中。

* 方法1：

如果用户经常使用`conda`管理各类环境，可以将其添加到用户环境变量，即将下面的环境变量添加到`~/.bashrc`的**最后一行**。添加后再在Terminal命令行中执行`source ~/.bashrc`，这样之后，包括登录节点和计算节点在内的所有节点都可以直接使用`conda`了。

```bash
export PATH="/opt/app/anaconda3/bin:$PATH"
```
* 方法2：
  
可以使用`module`，每次使用前，将`conda`添加到环境变量：

```bash
module load anaconda3
```

这种方法只是在每次使用时有效，登录到任何一个计算节点，还需要重新执行一遍`module load anaconda3`。

### 创建环境并安装包

用户可以使用`conda`创建属于自己版本的TensorFlow，环境名为`tf22`：

```
$ conda create --name tf22 python=3.8
```

相关包将安装到`/home/your-id/.conda/envs/...`下。

激活这个环境：

```
$ source activate tf22
```

在环境上安装 TensorFlow：

```
conda install tensorflow-gpu -n tf22
```

## 在共享集群上提交作业

### 在Slurm中提交作业

使用Slurm提交作业，首先编写一个作业提交脚本，将其命名为：`test.sh`：

```bash
#!/bin/bash

### 给你这个作业起个名字，方便识别不同的作业
#SBATCH --job-name=tf-example

### 指定该作业需要多少个节点
#SBATCH --nodes=1

### 指定该作业需要多少个CPU
#SBATCH --ntasks=16

### 队列名，目前可用的GPU队列为tesla和titan
#SBATCH --partition=titan

### 使用GPU数
#SBATCH --gres=gpu:1

echo $CUDA_VISIBLE_DEVICES

### Anaconda
export PATH=/opt/app/anaconda3/bin:$PATH

### 假设创建的conda环境名为 tf22
source activate tf22

python tf_test.py
```

然后在命令行中使用`sbatch`提交作业：

```
$ sbatch test.sh
```

### 多卡并行

如果需要多个节点多个GPU进行并行计算，则需要在Python代码中添加TensorFlow提供的分布式计算API：

```python
tf.distribute.cluster_resolver.SlurmClusterResolver(
    jobs=None, port_base=8888, gpus_per_node=None, gpus_per_task=None,
    tasks_per_node=None, auto_set_gpu=True, rpc_layer='grpc'
)
```

TensorFlow 多卡并行 API：

[https://tensorflow.google.cn/api_docs/python/tf/distribute/cluster_resolver/SlurmClusterResolver](https://tensorflow.google.cn/api_docs/python/tf/distribute/cluster_resolver/SlurmClusterResolver)

同时，在Slurm申请资源时要根据需求申请多节点多GPU卡资源。