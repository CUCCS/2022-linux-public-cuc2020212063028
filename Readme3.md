# 实验三
## 实验环境
Ubuntu 20.04

## 实验问题
- 如何添加一个用户并使其具备sudo执行程序的权限？

- 如何将一个用户添加到一个用户组？

- 如何查看当前系统的分区表和文件系统详细信息？

- 如何实现开机自动挂载Virtualbox的共享目录分区？

- 基于LVM（逻辑分卷管理）的分区如何实现动态扩容和缩减容量？

- 如何通过systemd设置实现在网络连通时运行一个指定脚本，在网络断开时运行另一个脚本？

- 如何通过systemd设置实现一个脚本在任何情况下被杀死后会立即重新启动？实现杀不死？

## 实验结果

### 如何添加一个用户并使其具备sudo执行程序的权限？
sudo adduser dai

sudo usermod -G sudo dai
asciicast

groups dai

Add_user


![31.jpg](../_resources/31.jpg)



### 如何将一个用户添加到一个用户组
usermod -a -G <group_name> <user_name>

User_groups


![32.jpg](../_resources/32.jpg)


### 如何查看当前系统的分区表和文件系统详细信息？
df -h

df-check


![33df.jpg](../_resources/33df.jpg)

sudo fdisk -l


![33fd.jpg](../_resources/33fd.jpg)



### 如何实现开机自动挂载Virtualbox的共享目录分区？
在windows上创建一个共享文件夹，我的目录为C:\Users\28653\Desktop\share

新建共享文件夹的配置情况


![34.jpg](../_resources/34.jpg)


sudo mkdir /mnt/share

sudo mount -t vboxsf share /mnt/share/

cd /mnt/share
cat_info

sudo vim /etc/fstab

修改相关配置，添加上最后一行代码，并保存退出

start_disposition

### 基于LVM（逻辑分卷管理）的分区如何实现动态扩容和缩减容量
1. 动态扩容：lvresize --size +{{SIZE}} --resizefs {{volume_group}}/{{logical_volume}}
2. 动态缩减：lvresize --size -{{SIZE}} --resizefs {{volume_group}}/{{logical_volume}}。

### 如何通过systemd设置实现在网络连通时运行一个指定脚本，在网络断开时运行另一个脚本？
修改systemd-networkd.service配置文件

[Service]
...
ExecStart=<path_service1> start
ExecStop=<path_service2> stop
...

### 如何通过systemd设置实现一个脚本在任何情况下被杀死之后会立即重新启动？实现杀不死？
修改一下相应脚本的服务配置文件，创建配置文件时在[Service]内加上“Restart=always”

服务脚本配置：

[Service]
...
Restart=always
...

再重新启动该服务即可



