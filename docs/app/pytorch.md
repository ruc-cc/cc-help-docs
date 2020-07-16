# PyTorch

PyTorch 是一个开源的Python机器学习库。我们主要使用`conda`来管理PyTorch环境。

!!! tip "提示"
    在计算云上，我们强烈建议用户使用 Anaconda 来管理和使用Python和R。我们已经在计算云的共享集群和交互实例上都安装好了 Anaconda 。`conda` 命令的使用方法可以详见我们提供的[入门教程](conda.md)。

## 在公共集群上使用

### 安装

用户可以使用`conda`创建属于自己版本的PyTorch，环境名为`torch`：

```
$ conda create --name torch python=3.8
```

相关包将安装到`/home/your-id/.conda/envs/...`下。

激活这个环境：

```
$ source activate torch
```

在环境上安装 PyTorch：

```
$ conda install pytorch torchvision cudatoolkit=10.1 -c pytorch -n torch
```

### 在Slurm中提交作业

使用 Slurm 提交作业，首先编写一个作业提交脚本，将其命名为：`test.sh`：

```bash
#!/bin/bash

### 给你这个作业起个名字，方便识别不同的作业
#SBATCH --job-name=pytorch-example

### 指定该作业需要多少个节点
#SBATCH --nodes=1

### 指定该作业需要多少个CPU
#SBATCH --ntasks=16

### 队列名
#SBATCH --partition=titan

### 使用GPU数
#SBATCH --gres=gpu:1

echo $CUDA_VISIBLE_DEVICES

# 使用刚刚创建的torch环境
export PATH=/opt/app/anaconda3/bin:$PATH
conda activate torch

python pytorch_test.py
```

然后在命令行中使用`sbatch`提交作业：

```
$ sbatch test.sh
```

### 多卡并行

如果需要多个节点提供计算，需要在代码中加入多机多卡的API。

```python
trainer = Trainer(gpus=8, num_nodes=4, distributed_backend=’ddp’)
```

详细信息参考官方API文档：

[https://pytorch-lightning.readthedocs.io/en/latest/slurm.html?highlight=slurm](https://pytorch-lightning.readthedocs.io/en/latest/slurm.html?highlight=slurm)