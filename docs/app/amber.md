# Amber

[Amber](http://ambermd.org/)是一款分子动力学模拟软件。

我们已经在共享集群上安装了Amber 18，并提供了CPU和GPU版本，安装位置为：`/opt/app/Amber/`。CPU版本和GPU版本分为两个文件夹，CPU版本路径地址为：`/opt/app/Amber/18`；GPU版本路径地址为：`/opt/app/Amber/18_gpu`。在共享集群上必须通过Slurm调度软件提交作业。每个路径下面有作业提交样例，文件名为：`run_amber.sh`。比如，提交一个GPU版本的作业，需要编写脚本为：

```bash
#!/bin/bash

#SBATCH --job-name=amber
#SBATCH --nodes=1
#SBATCH --ntasks=2

### GPU partition: titan/tesla
#SBATCH --partition=titan
#SBATCH --gres=gpu:2

. /opt/intel/bin/compilervars.sh intel64
. /opt/app/Amber/18_gpu/amber.sh

export LD_LIBRARY_PATH=/opt/pkgs/cuda/cuda-toolkit/lib64:$LD_LIBRARY_PATH

mpirun /opt/app/Amber/18_gpu/bin/pmemd.cuda.MPI -O -i md.in -o md1.out -p 5myr_w.prmtop -c eq5.rst -r md1.rst -x md1.mdcrd
```

将该脚本命名为`run_amber.sh`，然后通过`sbatch run_amber.sh`来提交这个作业。



