## 1. 脚本参数

提交作业的脚本中可以设置一些更多参数：

| 类型             	| 使用介绍                                                                           	| 参数示例             	|
|------------------	|------------------------------------------------------------------------------------	|----------------------	|
| 队列             	| 使用某个队列                                                                       	| --partition=cpu      	|
| 节点数           	| 申请几个节点，程序中未使用MPI或NCCL等并行计算框架，此处写1！                       	| --nodes=1            	|
| 进程数           	| 当前作业总共有多少个进程，或者说使用多少个CPU核心，一般根据队列中CPU核心数设置。   	| --ntasks=24          	|
| 每个节点的进程数 	| 每个节点所需进程数，或者说每个节点使用多少个CPU核心，一般根据队列中CPU核心数设置。 	| --ntasks-per-node=24 	|
| 时间             	| 最长需要多长时间，避免作业长时间计费                                               	| --time=12:00:00      	|
| 作业名           	| 给作业起名，方便在`squeue`时区分哪个作业                                           	| --job-name=jobname   	|
| GPU卡            	| 申请几块GPU卡，程序中未使用多卡并行，此处写1！                                     	| --gres=gpu:1         	|
| 输出             	| 将标准输出流输出到文件                                                             	| --output=xyz.out     	|
| 输出             	| 将标准错误流输出到文件                                                             	| --error=abc.out      	|

更多的作业参数信息，请参考[官方文档](https://SLURM.schedmd.com/sbatch.html)。

## 2. squeue

#### 2.1 输出更详细的作业信息

查看更加详细的作业信息：

```
squeue -l
```

或者自定义输出格式，数字为输出长度：

```
squeue -o "%.10i %.9P %.12j %.12u %.8T %.10M %.9l %.6D %R"
```

## 3. 作业状态

| Status     | Code | Explaination                                                 |
| ---------- | :--: | ------------------------------------------------------------ |
| COMPLETED  | `CD` | The job has completed successfully.                          |
| COMPLETING | `CG` | The job is finishing but some processes are still active.    |
| FAILED     | `F`  | The job terminated with a non-zero exit code and failed to execute. |
| PENDING    | `PD` | The job is waiting for resource allocation. It will eventually run. |
| PREEMPTED  | `PR` | The job was terminated because of preemption by another job. |
| RUNNING    | `R`  | The job currently is allocated to a node and is running.     |
| SUSPENDED  | `S`  | A running job has been stopped with its cores released to other jobs. |
| STOPPED    | `ST` | A running job has been stopped with its cores retained.      |

完成的作业状态文档，请参考[官方文档](https://SLURM.schedmd.com/squeue.html#lbAG)。

## 4. 作业未能运行原因

| Reason Code               | Explaination                                                 |
| ------------------------- | ------------------------------------------------------------ |
| `Priority`                | One or more higher priority jobs is in queue for running. Your job will eventually run. |
| `Resources`               | The job is waiting for resources to become available and will eventually run. |
| `QOSMaxGRESPerUser`       | Maximum number of General Resources(usually GPUs) for your job’s QoS have been met; job will run eventually. |
| `Dependency`              | This job is waiting for a dependent job to complete and will run afterwards. |
| `InvalidAccount`          | The job’s account is invalid. Cancel the job and rerun with correct account. |
| `InvaldQoS`               | The job’s QoS is invalid. Cancel the job and rerun with correct account. |
| `QOSGrpCpuLimit`          | All CPUs assigned to your job’s specified QoS are in use; job will run eventually. |
| `QOSGrpMaxJobsLimit`      | Maximum number of jobs for your job’s QoS have been met; job will run eventually. |
| `QOSGrpNodeLimit`         | All nodes assigned to your job’s specified QoS are in use; job will run eventually. |
| `PartitionCpuLimit`       | All CPUs assigned to your job’s specified partition are in use; job will run eventually. |
| `PartitionMaxJobsLimit`   | Maximum number of jobs for your job’s partition have been met; job will run eventually. |
| `PartitionNodeLimit`      | All nodes assigned to your job’s specified partition are in use; job will run eventually. |
| `AssociationCpuLimit`     | All CPUs assigned to your job’s specified association are in use; job will run eventually. |
| `AssociationMaxJobsLimit` | Maximum number of jobs for your job’s association have been met; job will run eventually. |
| `AssociationNodeLimit`    | All nodes assigned to your job’s specified association are in use; job will run eventually. |

更多的作业状态信息，请参考[官方文档](https://SLURM.schedmd.com/squeue.html#lbAF)。

