# Centos 8升级内核版本

文章目录
1. 查看当前内核版本
2. 使用ELRepo仓库
3. 安装最新版内核
4. 设置以新的内核启动
5. 生成grub配置文件并重启系统
6. 验证新内核
7. 查看系统中已安装的内核
8. 删除旧内核
9. 参考文献
1. 查看当前内核版本
使用的系统版本，当前日期CentOS最新版：

# 1 

''' cat /etc/redhat-release '''

2 CentOS Linux release 8.2.2004 (Core)
查看当前系统内核版本：

$ uname -r
4.18.0-193.6.3.el8_2.x86_64
当前日期 Linux 的内核很多都 5.x，各方面考虑还是有必要升级一下的，内核可以从这里直接下载：https://www.kernel.org/


2. 使用ELRepo仓库
这里使用ELRepo仓库，ELRepo 仓库是基于社区的用于企业级 Linux 仓库，提供对 RedHat Enterprise（RHEL）和其他基于 RHEL的 Linux 发行版（CentOS、Scientific、Fedora 等）的支持。ELRepo 聚焦于和硬件相关的软件包，包括文件系统驱动、显卡驱动、网络驱动、声卡驱动和摄像头驱动等。网址：http://elrepo.org/tiki/tiki-index.php :

导入ELRepo仓库的公共密钥：

$ rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
安装ELRepo仓库的yum源：

$ yum install https://www.elrepo.org/elrepo-release-8.el8.elrepo.noarch.rpm
可用的系统内核安装包：

复制代码
 1 $ yum --disablerepo="*" --enablerepo="elrepo-kernel" list available
 2 bpftool.x86_64 5.7.7-1.el8.elrepo elrepo-kernel
 3 kernel-ml-devel.x86_64 5.7.7-1.el8.elrepo elrepo-kernel
 4 kernel-ml-doc.noarch 5.7.7-1.el8.elrepo elrepo-kernel
 5 kernel-ml-headers.x86_64 5.7.7-1.el8.elrepo elrepo-kernel
 6 kernel-ml-modules-extra.x86_64 5.7.7-1.el8.elrepo elrepo-kernel
 7 kernel-ml-tools.x86_64 5.7.7-1.el8.elrepo elrepo-kernel
 8 kernel-ml-tools-libs.x86_64 5.7.7-1.el8.elrepo elrepo-kernel
 9 kernel-ml-tools-libs-devel.x86_64 5.7.7-1.el8.elrepo elrepo-kernel
10 perf.x86_64 5.7.7-1.el8.elrepo elrepo-kernel
11 python3-perf.x86_64 5.7.7-1.el8.elrepo elrepo-kernel
复制代码
3. 安装最新版内核

1 $ yum --enablerepo=elrepo-kernel install kernel-ml
4. 设置以新的内核启动
0 表示最新安装的内核，设置为 0 表示以新版本内核启动：

$ grub2-set-default 0
以后不需要第5步，直接使用这条指定不同数字设置不同内核版本启动。

5. 生成grub配置文件并重启系统

1 $ grub2-mkconfig -o /boot/grub2/grub.cfg
2 $ reboot
这一步可以不用执行生成grub配置的命令，直接重启！

6. 验证新内核

1 $ uname -r
2 5.7.7-1.el8.elrepo.x86_64
这个版本就是本文第一张截图中稳定版 v5.7.7

7. 查看系统中已安装的内核
可以看到这里一共安装了3个版本的内核，分别是 v4.18.0-193.6.3 和 v4.18.0-147.5.1以及 v5.7.7-1。

复制代码
 1 $ rpm -qa | grep kernel
 2 kernel-core-4.18.0-193.6.3.el8_2.x86_64
 3 kernel-modules-4.18.0-147.5.1.el8_1.x86_64
 4 kernel-ml-modules-5.7.7-1.el8.elrepo.x86_64
 5 kernel-devel-4.18.0-147.5.1.el8_1.x86_64
 6 kernel-4.18.0-80.el8.x86_64
 7 kernel-tools-libs-4.18.0-193.6.3.el8_2.x86_64
 8 kernel-core-4.18.0-80.el8.x86_64
 9 kernel-4.18.0-147.5.1.el8_1.x86_64
10 kernel-modules-4.18.0-80.el8.x86_64
11 kernel-4.18.0-193.6.3.el8_2.x86_64
12 kernel-tools-4.18.0-193.6.3.el8_2.x86_64
13 kernel-ml-5.7.7-1.el8.elrepo.x86_64
14 kernel-headers-4.18.0-193.6.3.el8_2.x86_64
15 kernel-core-4.18.0-147.5.1.el8_1.x86_64
16 kernel-devel-4.18.0-193.6.3.el8_2.x86_64
17 kernel-modules-4.18.0-193.6.3.el8_2.x86_64
18 kernel-ml-core-5.7.7-1.el8.elrepo.x86_64
复制代码
8. 删除旧内核
删除旧内核，这一步是可选的。

$ yum remove kernel-core-4.18.0 kernel-devel-4.18.0 kernel-tools-libs-4.18.0 kernel-headers-4.18.0
再查看系统已安装的内核，确认旧内核版本已经全部删除：

1 $ rpm -qa | grep kernel
2 kernel-ml-modules-5.7.7-1.el8.elrepo.x86_64
3 kernel-ml-5.7.7-1.el8.elrepo.x86_64
4 kernel-ml-core-5.7.7-1.el8.elrepo.x86_64
也可以安装 yum-utils 工具，当系统安装的内核大于3个时，会自动删除旧的内核版本：

1 $ yum install yum-utils
删除旧的版本使用 package-cleanup 命令。

9. 参考文献

1 ELRepo官网：http://elrepo.org/tiki/index.php
2 Centos7升级内核版本：https://www.cnblogs.com/xzkzzz/p/9627658.html
