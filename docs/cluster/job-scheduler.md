# 作业调度

我们首先登陆到共享集群的登录节点，可以使用Web SSH，也可以使用SSH客户端。

## 查看集群状态

我们可以使用`sinfo`查看集群信息和状态。

```bash
$ sinfo
```

得到当前集群的队列信息：

```
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
tesla*       up   infinite      3   idle tesla[1-3]
titan        up   infinite      7   idle titan[1-7]
cpu          up   infinite      6   idle cpu[1-6]
fat          up   infinite      2   idle fat[1-2]
```

可以看到，我们有4个队列，每个队列的设备参数可以参考[页面](intro.md)中计算资源部分。

## 提交作业

在Slurm系统中，提交作业前需要先准备一个脚本，该脚本中会说明用户本次申请的资源。准备好脚本后，再使用`sbatch`提交作业。

```bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=32
#SBATCH --time=2:00:00
#SBATCH --job-name=JOBNAME

python test.py
```

