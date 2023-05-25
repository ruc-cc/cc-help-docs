# 常见问题 F.A.Q

## 1 登录类问题

#### 1.1 校外访问和VPN

校外访问请使用VPN客户端，前往[https://vpn.ruc.edu.cn/](https://vpn.ruc.edu.cn/)**下载VPN客户端（EasyConnect）**，使用客户端登录学校VPN，再使用浏览器或其他软件访问本集群各类服务。

浏览器地址栏中不要出现 webvpn.ruc.edu.cn 字样！

#### 1.2 noVNC encountered an error

出现错误：noVNC encountered an error，且出现 webvpn.ruc.edu.cn 字样。

解决方法：同 1.1 下载VPN客户端，登录VPN客户端后，就可以像在校内一样使用本集群。

#### 1.3 登录VPN客户端后访问本系统网站仍提示：“请先登录VPN系统”

出现错误：已经登录VPN，仍提示“请先登录VPN系统”。

解决方法：清空浏览器缓存，具体清空方法请百度，或更换新的浏览器；再登录本系统。

#### 1.4 VPN登录出现问题或错误

出现问题：VPN登录不了或掉线等问题。

解决方法：请电话咨询 010-62516251，或邮件联系 its@ruc.edu.cn 。

## 2 使用类问题

#### 2.1 无法创建应用实例，用户资源不足

出现错误：无法创建应用实例，用户资源不足 User quota not enough, exceed app count quota 5 of user

解决方法：每个用户最多可以创建5个交互实例，已经创建的，在“我的资源”->“资源回收”里恢复已有资源或删除不用资源。

#### 2.2 RStudio出现：Status code 504 return by RStudio Server when executing 'client_init'

出现错误：tatus code 504 return by RStudio Server when executing 'client_init'

解决方法：重启这个 RStudio 实例，参考[这个配置](instance/rstudio.md#5)，设置 RStudio 启动参数。
