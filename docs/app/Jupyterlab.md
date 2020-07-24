# JupyterLab

JupyterLab是下一代Jupyter Notebook，它集成了更多功能，是一个集成开发环境。我们提供了CPU版本和GPU版本的实例，其中，GPU版本的实例使用的显卡为Nvidia GeForce 2080 Ti。

## 申请实例

JupyterLab属于交互实例，在计算云上使用时，需要先申请资源。首先点击“申请资源”，然后在所有应用中过滤出Jupyter。我们准备了CPU版和GPU版的镜像，用户按需申请。

![申请JupyterLab交互实例](../images/apply_jupyterlab.png)

## 使用JupyterLab

启动好后，即可进入Jupyter交互界面。用户可以前往[JupyterLab的官方文档](https://jupyterlab.readthedocs.io/en/latest/)学习详细使用方法。

![JupyterLab交互界面](../images/jupyterlab.png)

!!! tip "提示"
    在计算云上，我们强烈建议用户使用 Anaconda 来管理和使用Python和R。我们已经在 Jupyter 交互实例上安装好了 Anaconda 。`conda` 命令的使用方法可以详见我们提供的[入门教程](conda.md)。

用户进入Jupyter后默认使用`/opt/conda/bin/`下的`conda`和`python`。其中，Python3为Anaconda`base`环境提供的Python。

![创建新的程序](../images/jupyterlab_launcher.png)

如果用户希望安装自己的软件包，需要启动一个Terminal，在Terminal中使用`conda install`或`pip install`命令来安装所需软件包。

```
# 使用 conda 安装
conda install numpy

# 使用 pip 安装
pip install lightgbm
```

如下图所示：

![在JupyterLab的Terminal中安装软件](../images/jupyterlab_conda_install.png)

安装好之后，我们就可以在Notebook中使用这些包了。

## 创建新的环境

如果想创建新的环境，并且在Notebook中使用这个环境，必须安装`ipykernel`包。`ipykernel`帮助我们管理多个环境中的Kernel。

```bash
conda create -n <env_name> python=3.7 ipykernel
```

上面的代码在创建环境时安装了Python 3.7，同时也安装了`ipykernel`包。`<env_name>` 即创建的环境名。建议以英文命名，且不加空格，名称两边不加尖括号“<>”。

如果创建环境时忘了安装`ipykernel`，也可以后续手动安装这个包：

```bash
conda install -n <env_name> ipykernel
```

激活环境：

```bash
source activate <env_name>
```

将环境写入Notebook的Kernel中：

```bash
python -m ipykernel install --user --name <env_name> --display-name "<env_name>"
```

这时，我们已经将新环境中的安装好。在“我的资源”中重启这个Jupyter实例，如下图所示，先点击停止，等待关停后点击启动：

![重启Jupyter实例](../images/jupyter_restart.png)

重启这个Jupyter实例后，点击Jupyter左上角的“+”就可以启动一个`env_name`环境的Notebook，也可以在“Kernel”->“Change Kernel...”将当前Notebook切换为`env_name`。

## 使用本地SSH客户端登录

如果想使用本机的SSH客户端登录，需要参考[SSH客户端免密登录](../manual/ssh.md)中提供的方法。

