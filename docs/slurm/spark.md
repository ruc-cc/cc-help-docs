# Spark

Apache Spark 是一款通用大数据处理框架，它提供了 Java、Scala、Python、R 几种接口。本页面将展示如何在 SLURM 上启动一个 Spark 集群并使用 PySpark 进行分布式计算。

## 1. Spark on SLURM

在 SLURM 上启动 Spark 与传统意义上的 Spark/Hadoop 集群稍有不同。主要做以下两部分：

1. 在 SLURM 集群上申请资源，启动一个 Spark 集群。
2. 向这个 Spark 集群上提交任务。

## 2. 安装 PySpark

可以通过 `conda` 或 `mamba` 创建一个 Spark 环境，在这个环境中安装 `pyspark`：

```bash
conda install -c conda-forge pyspark
```

## 3. SLURM 启动脚本

```bash
#!/bin/bash

#SBATCH --job-name=spark
#SBATCH --nodes=4
#SBATCH --cpus-per-task=24
#SBATCH --ntasks-per-node=1
#SBATCH --partition=cpu24c
#SBATCH --output=spark.log

## --------------------------------------
## 0. Preparation
## --------------------------------------

# load the Spark module
source activate /fs/fast/u20200002/envs/pyspark

# uncomment the following lines if you do not want debug info
# set -x

# identify the Spark cluster with the Slurm jobid
export SPARK_IDENT_STRING=$SLURM_JOBID

# prepare directories
export SPARK_WORKER_DIR=${SPARK_WORKER_DIR:-$HOME/.spark/worker}
export SPARK_LOG_DIR=${SPARK_LOG_DIR:-$HOME/.spark/logs}
export SPARK_LOCAL_DIRS=${SPARK_LOCAL_DIRS:-/tmp/spark}
mkdir -p $SPARK_LOG_DIR $SPARK_WORKER_DIR

# get all the nodes allocated by SLURM
nodes=$(scontrol show hostnames "$SLURM_JOB_NODELIST")
nodes_array=($nodes)

## --------------------------------------
## 1. Start the Spark cluster master
## --------------------------------------
head_node=${nodes_array[0]}
port=7077
echo "Starting MASTER $i at $head_node"
srun --nodes=1 --ntasks=1 -w "$head_node" spark-class org.apache.spark.deploy.master.Master -h ${head_node} -p ${port} &
sleep 5
MASTER_URL=spark://${head_node}:${port}

## --------------------------------------
## 2. Start the Spark cluster workers
## --------------------------------------

# get the resource details from the SLURM job
export SPARK_WORKER_CORES=${SLURM_CPUS_PER_TASK:-1}
export SPARK_MEM=$(( ${SLURM_MEM_PER_CPU:-4096} * ${SLURM_CPUS_PER_TASK:-1} ))M
export SPARK_DAEMON_MEMORY=$SPARK_MEM
export SPARK_WORKER_MEMORY=$SPARK_MEM
export SPARK_EXECUTOR_MEMORY=$SPARK_MEM

worker_num=$((SLURM_JOB_NUM_NODES - 1))
for ((i = 1; i <= worker_num; i++)); do
    node_i=${nodes_array[$i]}
    port_i=$((port + i))

    echo "Starting WORKER $i at $node_i"
    srun --nodes=1 --ntasks=1 -w "$node_i" --output=$SPARK_LOG_DIR/spark-%j-workers.out     --label spark-class org.apache.spark.deploy.worker.Worker ${MASTER_URL} &
done
# start the workers on each node allocated to the job
export SPARK_NO_DAEMONIZE=1

## --------------------------------------
## 3. Submit a task to the Spark cluster
## --------------------------------------

python my_pi.py --partitions 50000 \
            --master ${MASTER_URL} \
            --executor_cores 24 \
```

对应的 `my_pi.py` 程序：

```python
import argparse
from random import random
from operator import add

from pyspark.sql import SparkSession


if __name__ == "__main__":
    parser = argparse.ArgumentParser(description="spark pi")
    parser.add_argument("--partitions", type=int, default=2 , help="pi partitions")
    parser.add_argument("--master", type=str, help="Spark master ip address")
    parser.add_argument("--executor_cores", type=str, help='Number of cores for each Spark executor')
    args = parser.parse_args()

    master = args.master

    spark = SparkSession\
        .builder\
        .appName("PythonPi")\
        .master(master) \
        .config("spark.executor.cores", args.executor_cores) \
        .getOrCreate()        

    partitions = args.partitions
    n = 100000 * partitions

    def f(_: int) -> float:
        x = random() * 2 - 1
        y = random() * 2 - 1
        return 1 if x ** 2 + y ** 2 <= 1 else 0

    count = spark.sparkContext.parallelize(range(1, n + 1), partitions).map(f).reduce(add)
    print("Pi is roughly %f" % (4.0 * count / n))

    spark.stop()
```