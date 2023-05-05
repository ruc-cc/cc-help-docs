# conda / mamba

`conda` 提供了包（Package）管理与环境（Environment）管理的功能，可以很方便地解决多版本多环境并存的问题。用户可以为某项具体的任务创建单独的环境，环境之间相互隔离。这样可以避免同一环境中各类软件相互冲突的问题。Anaconda 利用`conda`命令来进行包和环境的管理，并且已经包含了Python和相关的配套工具。

`mamba` 是一个加速 `conda` 的解决方案，它提供与 `conda` 的同样的功能。以下几乎所有命令，都可以将 `conda` 替换为 `mamba`。

具体而言，`conda` 和 `mamba` 提供了如下功能：

1. 可创建和管理多个环境，每个环境相互独立，互不影响，避免出现相互冲突的问题。
2. 有人将编译好的软件上传到 [anaconda.org](https://anaconda.org/) 上，用户在执行 `conda install` 命令时，其实是从 anaconda.org 上下载别人编译好的软件并安装到自己的环境里。
3. 在安装任何软件时，`conda` 尽量去检查当前环境中的软件与新安装软件的版本是否适配，并尽量适配软件的版本。

!!! tip "提示"
    在计算平台上，我们强烈建议用户使用 `conda` 来管理和使用Python。我们已经在计算平台的共享集群和 Jupyter 交互实例上都安装好了 `conda` ，用户只需要根据自身需要安装所需软件。

## 1. conda与环境变量

无论是共享集群还是独占实例，`conda`和`mamba`都安装在了 `/opt/app/anaconda3` 下。

独占实例（JupyterLab、VSCode）和共享集群是两个相互独立的存储空间，独占实例（JupyterLab、VSCode）中安装的环境和包目前和共享集群不共用。


### 1.2 共享集群

共享集群的`conda`位于`/opt/app/anaconda3/bin/conda`。

* 方法1：

如果用户经常使用`conda`管理各类环境，可以将其添加到用户环境变量，即将下面的环境变量添加到`~/.bashrc`的**最后一行**。添加后再在Terminal命令行中执行`source ~/.bashrc`，这样之后，包括登录节点和计算节点在内的所有节点都可以直接使用`conda`了。

```bash
export PATH="/opt/app/anaconda3/bin:$PATH"
```
* 方法2：
  
可以使用`module`模块，每次执行下面的命令，将`conda`添加到环境变量：

```bash
module load conda
```

这种方法只是在每次使用时有效，登录到任何一个计算节点，还需要重新执行一遍`module load conda`。

## 2. 添加源

Anaconda 默认的软件源在国外，速度比较慢，建议更换为清华源等国内的源：

```bash
# Anaconda官方库镜像
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/

# Anaconda第三方库 conda-forge 镜像
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
```

或根据清华源[Anaconda 镜像使用帮助](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)的提示更改源。

## 3. 环境管理

使用Anaconda，默认情况下是在`base`环境中，`base`环境只有一些基础的工具，而且用户**没有**权限读写该环境，所以需要**创建新的环境**，在新环境下安装软件。

### 3.1 创建新环境

```bash
# 交互实例（jupyterlab、vscode、远程桌面等）
mamba create -n <env_name> <package_names>

# SLURM共享集群 /fs/fast 下速度更快
# 第一次创建环境需要执行 mkdir
mkdir /fs/fast/uxxxx/envs
mamba create --prefix=/fs/fast/uxxxx/envs/<env_name> <package_names>
```

`<env_name>` 即创建的环境名。建议以英文命名，且不加空格，名称两边不加尖括号“<>”。

`<package_names>` 即安装在环境中的包名。名称两边不加尖括号“<>”。如果要在新创建的环境中创建多个包，则直接在`<package_names>`后以空格隔开，添加多个包名即可。

例如，创建一个名为`python3`的环境，环境中安装版本为3.10的python：

```bash
# 交互实例（jupyterlab、vscode、远程桌面等）
mamba create -n python3 python=3.10

# SLURM共享集群 /fs/fast 下速度更快
mamba create --prefix=/fs/fast/u20200002/envs/python3 python=3.10
```

`mamba create -n python3 python=3.10` 这样创建的环境以及环境内的包会被安装到 `/home/your-id/.conda/envs/python3` 目录下。

`mamba create --prefix=/fs/fast/u20200002/envs/python3` 会把软件安装到 `/fs/fast/u20200002/envs/python3` 下。

### 3.2 切换环境

切换环境：

```bash
source activate <env_name>

# SLURM共享集群，安装到 /fs/fast/u20200002/envs/python3
source activate /fs/fast/u20200002/envs/<env_name>
```

当成功切换环境之后，在该行行首将以“(env_name)”开头。其中，“env_name”为切换到的环境名。

退出环境：

```bash
source deactivate
```

### 3.3 显示已有环境

```bash
conda info --envs
```

### 3.4 删除环境

```bash
conda remove --name <env_name> --all
```

注意： <env_name> 为被删除环境的名称。环境名两边不加尖括号“<>”。

## 4. 安装包

### 4.1 在指定环境中安装包

安装包之前注意要先 `source activate <env_name>` 切换到该环境。`<env_name>` 即将包安装的指定环境名。环境名两边不加尖括号“<>”。

```bash
# 进入 env_name 环境
source activate <env_name>

# SLURM共享集群
source acitivate /fs/fast/uxxxx/envs/<env_name>

# 安装所需软件
conda install -c <channel_name> <package_name>

# 或者使用 mamba
mamba install -c <channel_name> <package_name>
```

`-c <channel_name>` 为指定包所在的channel，`<package_name>` 即要安装的包名。包名两边不加尖括号“<>”。

Anaconda 有一个默认（default）的 channel，是 Anaconda 官方维护的。同时也有很多人或者组织开辟了自己 channel，自己编译软件并将软件上传到该 channel。例如，PyTorch 团队维护着名为 `pytorch` 的channel。使用 `pytorch` 这个channel，安装该channel内的包：

```bash
conda install pytorch -c pytorch

# 或者使用 mamba
mamba install pytorch -c pytorch
```

对于某个特定的包，如何安装呢？一般遵循以下步骤：

1. 先前往 [anaconda.org](https://anaconda.org/)，在搜索框搜索包名。
2. 在搜索结果中选择下载量比较大的 channel，点击进入后显示有安装命令。

当前比较大的 channel 有 `conda-forge`，这是一个 Anaconda 官方以及其他第三方开源组织共同维护的 channel。

### 4.2 获取当前环境中已安装的包信息

```bash
conda list
```

### 4.3 卸载包

```bash
conda remove <package_name>
```

## 5. conda v.s. pip

相比 `conda`，`pip` 可以安装的包更多。用户可以先切换到所需环境，再在环境中执行 `pip install <package_name>`。

conda

|                | Conda                   | pip                      |
| -------------- | ----------------------- | ------------------------ |
| 源           | [anaconda.org](https://anaconda.org/)，包数量远少于PyPI     | [PyPI](https://pypi.org/)，Python包会被优先发布到PyPI上 |
| 包内容            | 他人已编译好的，可直接拿来使用的二进制                     | 绝大多数是Python源码，一小部分有C/C+代码的还需要下载后再编译                   |
| 支持语言           | Python、R、C/C++等         | 只支持Python                |
| 多环境管理          | 可以创建多个环境，环境内包含Python、R、C++等 | 本身不支持，需要依赖其他工具           |
| 依赖检查           | 严格的依赖检查                 | 依赖检查不严格                  |

!!! tip "建议"
    建议绝大多数基础的包尽量用 `conda` 安装，比如 Python 、PyTorch 等，其他一些比较小众的包可自己用 `pip` 安装。

## 6. conda 与 R

`conda` 也可以安装R以及各类R语言的包。

例如，想安装某个版本（本例为4.2）的R。

创建环境：

```
conda create -n r42

# SLURM共享集群上 /fs/fast 速度更快
conda create --prefix /fs/fast/uxxx/envs/r42
```

```
source activate r42

# SLURM共享集群上 /fs/fast 速度更快
source activate /fs/fast/uxxx/envs/r42
```

使用 conda 或者 mamba 安装：

```
conda install -c conda-forge r-base=4.2

mamba install -c conda-forge r-base=4.2
```

执行完以上命令后，就在 `r42` 环境下安装了 4.2 版本的R。 

如果想使用 `conda` 安装R包，在 [anaconda.org](https://anaconda.org/) 上搜索，比如`data.table`包，添加一个"r-"的前缀：`r-data.table`，点进搜索结果，找到安装命令

与在R语言中直接 `install.packages` 不同的是：

|                | Conda                   | install.packages                      |
| -------------- | ----------------------- | ------------------------ |
| 源           | [anaconda.org](https://anaconda.org/)，包数量远少于PyPI     | [CRAN](https://cran.r-project.org/)，R包会被优先发布到CRAN上 |
| 包内容            | 他人已编译好的，可直接拿来使用的二进制                     | 绝大多数是R源码，一小部分有C/C+代码还需要下载后再编译                   |
| 支持语言           | Python、R、C/C++等         | 只支持R                |
| 多环境管理          | 可以创建多个环境，环境内包含Python、R、C++等 | 本身不支持，需要依赖其他工具           |
| 依赖检查           | 严格的依赖检查                 | 依赖检查不严格                  |

!!! tip "建议"
    建议绝大多数基础的包尽量用 `conda` 安装，比如 RCpp 等，其他一些比较小众的包可自己用 `install.packages` 安装。

## 7. 其他软件

除此之外，anaconda.org 里包含的软件很多，比如NVIDIA的 CUDA、NCCL，Intel的 oneAPI。只要能在 anaconda.org 中能搜索到，就可以安装到本地的环境里。

最后软件会被安装到 `~/.conda/envs/<env_name>` 。SLURM共享集群里，如果设置了 `--prefix`，会安装到 `/fs/fast/uxxxx/envs/<env_name>` 目录下。