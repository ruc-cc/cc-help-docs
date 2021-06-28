# 使用SSH客户端

首先，我们需要获取目标实例的SSH登录信息。在“我的资源”中找到自己的实例，这里以“public_cluster”为例，点击右边的SSH连接信息，会显示该实例的IP和端口号：

![目标实例的各项操作](../images/public_cluster.png)

## VSCode

VSCode、PyCharm等IDE提供了SSH登录的功能，十分方便。VSCode可免费下载，再安装Remote插件，非常适合用来开发调试。

## 免密码登录

SSH免密码登录需要一对密钥对，包括一个公钥和一个私钥，其中私钥放在用户本机，公钥放在目标实例的`~/.ssh/authorized_keys`目录。下次登录时，用户本机的私钥和远程集群的公钥通过加密协议验证配对，验证成功后将不需要密码直接登录成功。所以这里需要生成公私钥，并将公钥上传到目标实例的指定位置。

使用SSH客户端免密码登录主要需要两步：

1. 在用户本机生成公私钥
2. 将公钥添加到计算平台目标实例的`~/.ssh/authorized_keys`文件末尾

### macOS & Linux

在用户本机生成公钥和私钥的命令为

```bash
ssh-keygen -t rsa
```

这时终端会提示

```bash
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/~your-local-username~/.ssh/id_rsa):
```

括号内为生成的公私钥的默认目录位置，直接回车就会使用这个默认位置。如果默认位置已经生成过公私钥，则终端会提示是否需要覆盖，这时可不用再次生成公私钥。

```bash
/Users/~your-local-username~/.ssh/id_rsa already exists.
Overwrite (y/n)?
```

终端会提示输入密码 passphrase，这个密码为生成私钥的密码，将来防止私钥被其他人盗用。这里可以不输入任何密码，直接回车，再次提示输入密码，再次回车。

这时公钥存储在 `/Users/~your-local-username~/.ssh/id_rsa.pub` 文件里，私钥存储在 `/Users/~your-local-username~/.ssh/id_rsa` 文件里。

获取公钥：

```bash
cat id_rsa.pub
```

将返回值拷贝到剪贴板。接下来需要我们将公钥追加到计算平台目标实例 `~/.ssh/authorized_keys`。使用Web SSH登录：

```bash
echo "ssh-rsa AAAA..." >> ~/.ssh/authorized_keys
```

其中，"ssh-rsa AAAA..."为你本机的公钥，刚刚我们已经拷贝到剪贴板。

### Windows

我们提供了一个[MobaXterm](./../files/MobaXterm_Portable_v20.3.zip)的校内下载链接。

MobaXterm也提供了生成公私钥的功能。

