# RStudio

RStudio 是 RStudio 公司打造的一款基于 Web 的开源IDE。计算云提供了多人共享模式的RStudio容器实例，可供多人团队共享使用。

## 创建共享项目

RStudio实例可以被多人共享，可以由课题组中一人创建，并邀请课题组其他人加入该RStudio实例。我们只对创建人进行计费。

要创建共享模式使用的实例，需要先创建**共享项目**：

![创建共享项目](../images/create_share_project.png)

![创建共享项目对话](../images/create_share_project_dialog.png)

计算云后端会为**共享项目**在系统中创建一个用户组，并给这个用户组在共享文件系统上分配一个组Home目录。**共享项目**适用于某个团队或某个课题组共用某些实例的场景。

### 邀请其他用户共享使用

如果希望其他用户共享使用此实例，则可以在此共享项目界面的**用户**子页面中**邀请**其他用户加入此共享项目。点击界面右上角的用户名，可以看到用户ID。邀请前，需要先与该用户沟通，获取该用户ID，然后根据用户ID搜索到用户。

![邀请加入项目](../images/invite_to_share_project.png)

当然，我们也可以创建一个共享项目，共享项目中只有自己本人。

## 申请RStudio应用实例

RStudio属于交互实例，在**申请资源**界面里申请：

![申请RStudio实例](../images/create_rstudio_instance.png)
![申请RStudio实例对话](../images/create_rstudio_instance_dialog.png)

## 启动与登录

![启动RStudio实例](../images/start_rstudio_instance.png)

由于RStudio是共享交互实例，需要用计算云的用户名和密码登录。

![登录RStudio实例](../images/rstudio_login.png)

## 简单示例

![RStudio示例](../images/rstudio_example.png)