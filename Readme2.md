# 实验二
## 软件环境
- 本地虚拟机环境（Ubuntu 20.04）


## 实验问题
- 使用表格方式记录至少 2 个不同 Linux 发行版本上以下信息的获取方法，使用 asciinema 录屏方式「分段」记录相关信息的获取过程和结果
- 【软件包管理】在目标发行版上安装 tmux 和 tshark ；查看这 2 个软件被安装到哪些路径；卸载 tshark ；验证 tshark 卸载结果
- 【文件管理】复制以下 shell 代码到终端运行，在目标 Linux 发行版系统中构造测试数据集，然后回答以下问题：
	- 找到 /tmp 目录及其所有子目录下，文件名包含 666 的所有文件
	- 找到 /tmp 目录及其所有子目录下，文件内容包含 666 的所有文件
- 【文件压缩与解压缩】练习课件中 文件压缩与解压缩 一节所有提到的压缩与解压缩命令的使用方法
- 【跟练】 子进程管理实验
- 【硬件信息获取】目标系统的 CPU、内存大小、硬盘数量与硬盘容量


## 实验结果
- 使用表格方式记录至少 2 个不同 Linux 发行版本上以下信息的获取方法，使用 asciinema录屏方式「分段」记录相关信息的获取过程和结果
 
 | 版本号|	ubuntu 20.04|CentOS 7.7 | 
| :-----:| :----: | :----: |
| 安装|sudo apt install XXX|yum install xxx |
| 安装路径|which XXX|which xxx |
| 卸载|sudo apt-get remove --purge XXX|yum -y remove xxx |
| 验证卸载|which|which xxx |
| 找特殊文件名的文件|sudo find ./ -name 'XXX'|sudo find ./ -name 'XXX |
| 找特殊内容的文件|sudo grep -r 'XXX' ./|sudo grep -r 'XXX' ./ |
| CPU信息获取|cat /proc/cpuinfo|cat /proc/cpuinfo |
| 内存大小|free -h|free -h |
| 硬盘数量以及容量|df -h/ lsblk|df -h/ lsblk |



在本地虚拟机安装 asciinema ：

sudo apt-get update
sudo apt-get install asciinema
asciinema auth


在云环境安装asciinema ：
yum install -y  asciinema
asciinema auth


- 【软件包管理】在目标发行版上安装 tmux 和 tshark ；查看这 2 个软件被安装到哪些路径；卸载 tshark ；验证 tshark 卸载结果 ：
在本地虚拟机操作：
tmux的安装以及查看路径：
sudo apt-get install tmux
which tmux
[![asciicast](https://asciinema.org/a/FAa4yoyFGNGnaQNTCtzGGrQlp.svg)](https://asciinema.org/a/FAa4yoyFGNGnaQNTCtzGGrQlp)
tshark的安装卸载、路径查看
sudo apt-get install tshark
which tshark
sudo apt-get remove --purge tshark
[![asciicast](https://asciinema.org/a/n5eds4VYaZU8r6gUzEeXZ3uv7.svg)](https://asciinema.org/a/n5eds4VYaZU8r6gUzEeXZ3uv7)
在云环境操作：
tmux的安装以及查看路径：
yum install tmux
which tmux


- 【文件管理】复制以下 shell 代码到终端运行，在目标 Linux 发行版系统中构造测试数据集，然后回答以下问题：
	- 找到 /tmp 目录及其所有子目录下，文件名包含 666 的所有文件
	- 找到 /tmp 目录及其所有子目录下，文件内容包含 666 的所有文件
本地虚拟机操作:
cd /tmp && for i in $(seq 0 1024);do dir="test-$RANDOM";mkdir "$dir";echo "$RANDOM" > "$dir/$dir-$RANDOM";done

cd /tmp
sudo find ./ -name '*666*'
sudo grep -r --exclude=*.cast '666' .
[![asciicast](https://asciinema.org/a/lEK4rXMP3ctM28TM8KM9ZQZGY.svg)](https://asciinema.org/a/lEK4rXMP3ctM28TM8KM9ZQZGY)


- 【文件压缩与解压缩】练习课件中 文件压缩与解压缩 一节所有提到的压缩与解压缩命令的使用方法
本地虚拟机操作:
测试文件为 test.txt gzip:

gzip test.txt
gzip -d test.txt.gz
[![asciicast](https://asciinema.org/a/3ma6Vwbg5G6osJX39T0XlpF2u.svg)](https://asciinema.org/a/3ma6Vwbg5G6osJX39T0XlpF2u)

bzip2:

bzip2 -z test.txt
bzip2 -d test.txt.bz2

[![asciicast](https://asciinema.org/a/g5qSqMtVhEbBZpodDSKRmJ8pj.svg)](https://asciinema.org/a/g5qSqMtVhEbBZpodDSKRmJ8pj)

zip:

zip test.txt.zip /tmp
unzip test.txt.zip
[![asciicast](https://asciinema.org/a/HMy3hCz1DfcSfR1aDrk7zLVAs.svg)](https://asciinema.org/a/HMy3hCz1DfcSfR1aDrk7zLVAs)

tar：

tar -czvf test-1.tar.gz test.txt
tar -xzvf test-1.tar.gz

[![asciicast](https://asciinema.org/a/qR9Evwi14RwgKArKgqAihJM4i.svg)](https://asciinema.org/a/qR9Evwi14RwgKArKgqAihJM4i)

7z:

7za a test.7z /test.txt
7za x test.7z
[![asciicast](https://asciinema.org/a/QLcZbJlEGH32Pdw1l9F7ewMFY.svg)](https://asciinema.org/a/QLcZbJlEGH32Pdw1l9F7ewMFY)


rar:

首先在本地主机上发送文件到虚拟机

通过scp指令将rar文件传输给云平台

虚拟机执行 unrar x netclass.rar


云平台操作：
测试文件为test.txt
通过scp指令将测试文件传输到云平台,查看文件 cat ./test.txt

gzip:
gzip test.txt
gzip -d test.txt.gz

bzip2:
yum install bzip2
bzip2 -z test.txt
bzip2 -d test.txt.bz2

zip:
zip test.txt.zip /tmp
yum install unzip
unzip test.txt.zip

tar：
tar -czvf test-1.tar.gz test.txt
tar -xzvf test-1.tar.gz

7z:
yum install -y p7zip
7za a test.7z /test.txt
7za x test.7z

rar:
首先在本地上发送文件到云环境：

通过scp指令将rar文件传输给虚拟机scp 

安装rar：

下载安装包：wget --no-check-certificate http://www.rarlab.com/rar/rarlinux-x64-6.0.2.tar.gz

解压安装包到目录：tar xf rarlinux-x64-6.0.2.tar.gz -C /usr/local/

创建软连接：

ln -s /usr/local/rar/rar /usr/local/bin/rar
ln -s /usr/local/rar/unrar /usr/local/bin/unrar
云平台执行 unrar x netclass.rar


- 【硬件信息获取】目标系统的 CPU、内存大小、硬盘数量与硬盘容量
本地虚拟机：
获取CPU信息 cat /proc/cpuinfo 获取内存信息 free -h 获取硬盘数量以及容量信息df -h 
[![asciicast](https://asciinema.org/a/2e5lhKP72FYCxjrWBCmONSEdP.svg)](https://asciinema.org/a/2e5lhKP72FYCxjrWBCmONSEdP)

云平台：
获取方式与Ubuntu一致 获取CPU信息 cat /proc/cpuinfo 获取内存信息 free -h 获取硬盘数量以及容量信息df -h 

remote_checkinfo
asciinema
