# README

#### 1.调查并记录实验环境的如下信息：

- 当前 Linux 发行版基本信息

  通过在搜索引擎检索可发现使用`sudo lsb_release -a`就可以查询到发行版本信息

  ![](C:\Users\28653\Desktop\img\1.jpg)

- 当前 Linux 内核版本信息

  使用`uname -a`就可以获得内核信息

  ![](C:\Users\28653\Desktop\img\2.jpg)

#### 2.Virtualbox 安装完 Ubuntu 之后新添加的网卡如何实现系统开机自动启用和自动获取 IP？

检索之后发现ubuntu20网络的配置信息将不在保存在`/etc/network/interfaces`文件中，虽然该文件依然存在，但是内容是空的。                                                       新系统已经使用netplan管理网络，对于配置信息，使用`sudo nano/etc/netplan/00-installer-config.yaml`，内容如下

![](C:\Users\28653\Desktop\img\3.jpg)

检索得知可以看到网卡enp0s3下面有参数dhcp4: true，说明该网卡开启了dhch地址分配且有enp0s8。按照搜索执行`sudo netplan apply`生效

#### 3.如何使用 `scp` 在「虚拟机和宿主机之间」、「本机和远程 Linux 系统之间」传输文件？

浅看了一下，服务器需安装SSH服务器端opensshserver，客户端需安装SSH客户端openssh-client																											ssh-server：`sudo apt-get install openssh-server`
ssh-client：`sudo apt-get install openssh-client`

#### 4.如何配置 SSH 免密登录？

在客户端生成公私钥，再将公钥上传到服务器

#### 遇到的问题

1.登录的时候输密码看不见，以为出了什么问题，询问同学之后得知确实看不见，心里有数就行

2.一开始开虚拟机的时候显示网卡1不存在，不能打开，当时刚打开还比较懵随便选了一个别的，仍旧不行，直接选了跟网卡2相同的，仍然打不开任务（上述问题全部忘记截图了），去畅课的论坛逛了一圈发现有个同学跟我的问题相同，直接用她给出的解决方法顺利解决

3.

![](C:\Users\28653\Desktop\img\q1.jpg)并不知道这个东西怎么退出去，用我贫瘠的英语知识看应该是选exit，但是选yes之后怎么都出不去，想着啊这然后随便选了一下出来了