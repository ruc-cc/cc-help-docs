# MATLAB

MATLAB（矩阵实验室）是MATrix LABoratory的缩写，是一款由美国 MathWorks 公司出品的商业数学软件。MATLAB是一种用于算法开发、数据可视化、数据分析以及数值计算的高级技术计算语言和交互式环境。MATLAB主要用于数值运算，但利用为数众多的附加工具箱（Toolbox）它也适合不同领域的应用，例如控制系统设计与分析、图像处理、信号处理与通讯、金融建模和分析等。

## 交互实例

### 申请资源和启动

我们已经提供了交互实例版本的 MATLAB，如果需要图形化界面，请点击“申请资源”，寻找到名为“Matlab2019”的实例。接着给您的实例起个作业名，方便识别和记忆。

“分配中”，表示正在排队。“分配中”，表示正在排队。分配好资源后，点击启动按钮：

![启动远程桌面](../images/matlab_start.png)

启动好后，点击“远程桌面”，登录到远程桌面，进入远程桌面后，MATLAB已经在启动。

!!! tip "Linux MATLAB"
    本MATLAB为Linux操作系统下的MATLAB，其功能与绝大多数的Windows、macOS操作系统下的MATLAB使用习惯几乎一致，只是在文件夹目录结构稍有区别。详情参考[数据管理和文件传输](../manual/transfer.md#linux)部分。

## 并行加速

为了利用服务器的多个CPU核心，对于计算密集型的任务，可以考虑使用MATLAB提供的并行工具箱，下面做一些简单的介绍，更多使用方法请阅读官方文档：[Parallel Computing Toolbox][1]。

一般地，我们可以使用 `parfor` 来替换原来的 `for` 来做循环。大致步骤为：

1. 创建一个 MATLAB parpool
2. 初始 parpool 参数，包括并行处理器数，临时文件地址等
3. 将代码中所有 `for` 替换为 `parfor` 

一个使用 `parfor` 的简单例子：

```matlab
parpool('local', 32)

parfor i = 1:100
    A(i) = sin(i*2*pi/20);
end
```

注意，上面代码中的 `parpool()` 函数第二个参数要输入并行所使用的CPU核心数。一般是“申请资源”模块中，申请时填写的CPU核数。

[1]: https://ww2.mathworks.cn/products/parallel-computing.html