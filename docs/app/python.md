# Python

Python是数据分析、数据挖掘和机器学习领域经常使用的一种编程语言。在计算云平台上，有两种使用Python的方式：

1. Jupyter，使用方式详见[文档](Jupyterlab.md)
2. 在共享集群中提交作业，介绍和使用方式详见[文档](GPU-Cluster.md)

其中，第一种方式适合对Linux不太熟悉的用户，第二种方式适合有一定Linux基础，同时计算量较大，可以同时提交多个作业，以验证不同的参数。我们建议新人可以从Jupyter开始上手，如果发现资源需求较大，可逐渐将工作模式迁移到第二种方式上。

## Python包安装

!!! warning "Jupyter与共享集群"
    共享集群与Jupyter等交互实例是相互独立的，在Jupyter中安装的一些软件包，并不能直接在共享集群里直接使用，两边都需要单独安装。

### conda与环境变量

!!! tip "提示"
    在计算云上，我们强烈建议用户使用 Anaconda 来管理和使用Python和R。我们已经在计算云的共享集群和交互实例上都安装好了 Anaconda 。`conda` 命令的使用方法可以详见我们提供的[入门教程](conda.md)。

#### JupyterLab

在JupyterLab中，我们已经安装好了 `conda`，可以直接在Terminal中使用，其位置位于：`/opt/conda/bin/conda`。

#### 共享集群

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

我们强烈建议用户可以使用`conda`创建属于自己版本的Python，这样有几个好处：

1. Python生态中各个包版本迭代速度很快，基于不同的环境，可以使用不同的包版本。
2. 可以为某项任务、某个工程创立单独的环境，环境之间相互隔离，方便相互切换。
3. 如果不创建单独环境，当默认环境中的包越来越多时，包与包之间会产生一些冲突，导致整个环境不可用。创建单独的环境可以避免包冲突。

比如，我们想创建Python3.8、TensorFlow 2.2的环境，环境名为`tf22`：

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