# conda

[Anaconda][1] 是一个用于科学计算的Python发行版，支持 Linux、Mac、 Windows系统以及 Python、R等科学计算语言，提供了包（Package）管理与环境（Environment）管理的功能，可以很方便地解决多版本多环境并存的问题。用户可以为某项具体的任务创建单独的环境，环境之间相互隔离。这样可以避免同一环境中各类软件相互冲突的问题。Anaconda 利用`conda`命令来进行包和环境的管理，并且已经包含了Python和相关的配套工具。

!!! tip "提示"
    在计算云上，我们强烈建议用户使用 Anaconda 来管理和使用Python。我们已经在计算云的共享集群和 Jupyter 交互实例上都安装好了 Anaconda ，用户只需要根据自身需要安装所需软件。

## 软件与环境变量

一般情况下，我们主要在JupyterLab和共享集群里使用`conda`，JupyterLab和共享集群是两个相互独立的模块，JupyterLab中安装的环境和包目前和共享集群不共用。

### JupyterLab

在JupyterLab中，我们已经安装好了 `conda`，可以直接在Terminal中使用，其位置位于：`/opt/conda/bin/conda`。

### 共享集群

共享集群的`conda`位于`/opt/app/anaconda3/bin/conda`。

* 方法1：

如果用户经常使用`conda`管理的Python环境，可以将其添加到用户环境变量，即将下面的环境变量添加到`~/.bashrc`的**最后一行**。添加后再执行`source ~/.bashrc`，包括登录节点和计算节点在内的所有节点都可以直接使用`conda`了。

```bash
export PATH="/opt/app/anaconda3/bin:$PATH"
```
* 方法2：
  
可以使用`module`模块，每次使用前，将`conda`添加到环境变量：

```bash
module load anaconda3
```

这种方法只是在每次使用时有效，登录到任何一个计算节点，还需要重新执行一遍`module load anaconda3`。

## 添加源

Anaconda 默认的软件源在国外，速度比较慢，我们可以将其更换为清华源：

```bash
# Anaconda官方库镜像
$ conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
$ conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/

# Anaconda第三方库 conda-forge 镜像
$ conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
```

## 环境管理

使用Anaconda，默认情况下是在`base`环境中，`base`环境有一些基础的工具，可以直接在这个环境下安装软件，也可以创建新的环境，在新环境下安装软件。

### 创建新环境

```bash
$ conda create -n <env_name> <package_names>
```

`<env_name>` 即创建的环境名。建议以英文命名，且不加空格，名称两边不加尖括号“<>”。

`<package_names>` 即安装在环境中的包名。名称两边不加尖括号“<>”。如果要在新创建的环境中创建多个包，则直接在`<package_names>`后以空格隔开，添加多个包名即可。例如，创建一个名为`python3`的环境，环境中安装版本为3.7的python，同时也安装了`numpy`和`pandas`：

```bash
$ conda create -n python3 python=3.7 numpy pandas
```

新的环境以及环境内的包会被安装到`/home/your-id/.conda/envs/`目录下。

### 切换环境

切换环境：

```bash
$ source activate <env_name>
```

当成功切换环境之后，在该行行首将以“(env_name)”开头。其中，“env_name”为切换到的环境名。

退出环境：

```bash
$ source deactivate
```

### 显示环境

```bash
$ conda info --envs
```

### 删除环境

```bash
$ conda remove --name <env_name> --all
```

注意： <env_name> 为被删除环境的名称。环境名两边不加尖括号“<>”。

## 包管理

### 获取当前环境中已安装的包信息

```bash
$ conda list
```

### 在指定环境中安装包

```bash
$ conda install -n <env_name> <package_name>
```

注意：

1. `<env_name>` 即将包安装的指定环境名。环境名两边不加尖括号“<>”。
2. `<package_name>` 即要安装的包名。包名两边不加尖括号“<>”。
3. 不加`-n <env_name>`，则安装到当前所在的环境。

### 卸载包

```bash
$ conda remove -n <env_name> <package_name>
```

## pip

相比 Anaconda，pip 可以安装的包更多。用户可以先切换到所需环境，再在环境中执行 `pip install <package_name>`

[1]: https://www.anaconda.com/