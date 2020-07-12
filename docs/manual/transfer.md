# 文件传输

## 用户数据存储空间

在云平台上，系统会为用户在共享文件系统上分配两种目录：

  * 每个用户分配一个共享文件系统上的目录，作为个人交互式实例里的Home目录。
  
  * 对于加入共享项目组的用户，系统会为用户在每个项目组中分配一个Home目录。

## Web页面访问

### 访问个人Home目录

#### 上传文件

点击**我的资源**

![我的资源](../images/click_my_resource.png)

点击**数据管理**, 可以看到Home目录下的子目录和文件。

![数据管理](../images/click_my_resource_data.png)

点击**上传**, 会弹出上传文件对话框。

![数据管理](../images/click_upload_button.png)

![数据管理](../images/upload_dialog.png)

#### 下载文件

选中要下载的文件，然后点击**下载**按钮

![数据管理](../images/download_file.png)

### 访问项目Home目录

项目Home目录的访问方式与个人Home目录的访问类似，只是先要选择某个共享项目，进入项目页面。

![选择项目](../images/choose_a_project.png)

## 通过SSH账户访问

对于开放了SSH端口的集群或实例，用户可以通过SSH账户使用scp类的工具来传输数据。

以公共集群项目为例，用户在public_cluster共享项目的页面里，选择**科研实例**，可以看到名为`public_cluster`的实例：

![集群实例](../images/public_cluster_instance.png)

点击最右侧的**ssh**服务地址按钮，会弹出平台对外开放的IP地址和端口：

![集群实例](../images/ssh_ip_port.png)

用户在校内，能够直接访问平台服务IP地址的情况下，可以使用scp, winscp命令之类的工具来传输数据：

```bash
scp -P 20014 some_data u20200002@10.77.90.101:
```
