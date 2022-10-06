# Linux

虚拟机模式下用户无法使用InfiniBand的高速网络，计算节点之间、计算节点与存储之间的网络延迟略高。

## 首次使用重置密码

申请到虚拟机实例后，需要给这台虚拟机初始化root账户密码，如下图所示，之后再以root身份登录该虚拟机。

![虚拟机](../images/virtual_machine.png)

## 数据拷贝

平台内的虚拟机实例访问共享文件系统上的Home目录的机制类似于用户从外部访问WebDAV服务。目前平台上的虚拟机镜像预先做好了WebDAV卷的自动挂载，挂载点是/webdav。

```bash
# 登录刚申请的Linux虚拟机
ssh -p 20017 root@login.cc.ruc.edu.cn
# 查看 /webdav 目录
root@ubuntu:~# ls /webdav/
 MyData  'ProjectGroup(hpctest)'  'ProjectGroup(public_cluster)'   lost+found
```

!!! warning "括号前后要加单引号'"
    `ProjectGroup(public_cluster)` 这样的目录，在访问和拷贝数据时，需要加单引号，例如：`ProjectGroup'('public_cluster')'`