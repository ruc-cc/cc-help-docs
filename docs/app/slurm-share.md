## 课题组共享文件夹

按照[课题组团队管理](../manual/project.md)中的方式创建课题组，将相应用户拉入相应课题组，课题组内所有用户拥有组（group）访问权限。

在我们的共享集群中，`/home/share`为共享目录，可以在这里为课题组创建共享文件夹，设置文件夹的组权限为刚刚创建的课题组名，即可让该文件夹设置为课题组共享。考虑学生的流动性，这里建议使用老师的账号创建目录。

```bash
cd /home/share
mkdir [DIRECTORY_NAME]
chgrp [GROUP_NAME] [DIRECTORY_NAME]
```