# TensorFlow

TensorFlow 是一个端到端开源机器学习平台。它拥有一个全面而灵活的生态系统，其中包含各种工具、库和社区资源，可助力研究人员推动先进机器学习技术的发展，并使开发者能够轻松地构建和部署由机器学习提供支持的应用。

## 在公共集群上使用

用户可以使用`conda`创建属于自己版本的TensorFlow，环境名为`tf22`：

```
$ conda create --name tf22 python=3.8
```

相关包将安装到`/home/user-id/.conda/envs/...`下。

激活这个环境：

```
$ source activate tf22
```

在环境上安装 TensorFlow：

```
conda install tensorflow-gpu -n tf22
```

使用Slurm提交作业，首先编写一个作业提交脚本，将其命名为：`test.sh`：

```
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=4:00:00

### 队列名
#SBATCH --partition=telsa

### 使用GPU数量
#SBATCH --gres=gpu:1

echo $CUDA_VISIBLE_DEVICES

# 使用刚刚创建的tf2.2环境
export PATH=/opt/app/anaconda3/bin:$PATH
conda activate tf22

python tf_test.py
```

然后在命令行中使用`sbatch`提交作业：

```
$ sbatch test.sh
```