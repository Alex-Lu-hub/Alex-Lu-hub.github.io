---
title: Linux小系统网络配置问题
tags:  [Linux]
categories: [Linux]
date: 2020-10-25 16:00:00
updated: 2020-10-27 01:00:00
---

在裁剪好内核，和外围文件系统一起启动后，发现 ``service`` 命令以及网络配置都无法实现，进行调试。

<!-- more -->

#  ``service`` 命令使用后无任何输出

查看 ``/sbin/service`` ，其调用 ``/etc/init.d/functions`` 。为了定位问题所在，查看 ``functions`` ，发现它所有的错误输出都送到了 ``/dev/null`` （黑洞），没有显示出来。将所有的 ``2>/dev/null`` 删去，查看到了错误信息，缺少 ``/etc/profile.d/lang.sh`` 。为了避免再出现相同位置的文件缺失，直接将 ``/etc/profile.d``  拷入到小系统中，service能够正常的运行了。

#  ``service network`` 命令无法正常运行

在 ``service`` 能够运行后，使用 ``service network`` 、 ``service network start`` 等命令是无法正常运行的，会报错缺少某些命令或文件。按照报错信息将缺少的命令和文件补全。以下是我拷贝的一些：

```shell
env
sort
/bin/ipcalc
grep
/var/run/netreport
egrep
/sbin/arping
pidof
```

# ``service ssh`` 命令无法正常运行

完成上述两个步骤后，已经可以利用 ``service network`` 命令配置好小系统的网络了，从主机ping可以ping通相应的IP。但是直接用ssh还无法链接，同样进行调试，查看报错进行拷贝。以下是我拷贝的一些：

```shell
/usr/bin/dirname
```

拷贝完成后，现在运行 ``service ssh restart`` 只会提示：

```shell
Stopping sshd: [FAILED]
```

由于没有更多的报错信息，难以进行调试，查看 ``/etc/init.d/sshd`` 脚本内容：

```shell
vi /etc/init.d/sshd
```

在脚本开头可以看到一些说明信息：

```bash
#!/bin/bash
#
# sshd          Start up the OpenSSH server daemon
#
# chkconfig: 2345 55 25
# description: SSH is a protocol for secure remote shell access. \
#              This service starts up the OpenSSH server daemon.
#
# processname: sshd
# config: /etc/ssh/ssh_host_key
# config: /etc/ssh/ssh_host_key.pub
# config: /etc/ssh/ssh_random_seed
# config: /etc/ssh/sshd_config
# pidfile: /var/run/sshd.pid

### BEGIN INIT INFO
# Provides: sshd
# Required-Start: $local_fs $network $syslog
# Required-Stop: $local_fs $syslog
# Should-Start: $syslog
# Should-Stop: $network $syslog
# Default-Start: 2 3 4 5
# Default-Stop: 0 1 6
# Short-Description: Start up the OpenSSH server daemon
# Description:       SSH is a protocol for secure remote shell access.
#                    This service starts up the OpenSSH server daemon.
### END INIT INFO
```

可以看到相关的配置文件在 ``/etc/ssh`` 文件夹下，拷贝至小系统后再次尝试，可以看到新的有用报错信息了，继续拷贝。以下是我拷贝的一些命令：

```shell
usleep
sleep
```

# ssh无法连接

完成上面的操作后，ssh连接会提示输入密码，输入密码后会卡住在如下命令：

```shell
PTY allocation request failed on channel 0
```

但是在ssh连接命令上加上 ``-T`` 就可以顺利进行连接。

查阅说明：

```bash
-T Disable pseudo-tty allocation.
```

 ``-T`` 意思是禁止分配伪终端。当用ssh或telnet等登录系统时，系统分配给我们的终端就是伪终端。 如果ssh使用此选项登录系统时，由于禁用，将无法获得终端；但仍能够获得shell，只不过看起来像在本地，也没有很多应有的环境变量，例如命令提示符，PS1等。

于是猜测仍然是与ssh相关的依赖没有拷完。使用如下命令查看ssh依赖：

```shell
[root@localhost ~]# rpm -ql openssh
/etc/ssh
/etc/ssh/moduli
/usr/bin/ssh-keygen
/usr/libexec/openssh
/usr/libexec/openssh/ssh-keysign
/usr/share/doc/openssh-5.3p1
/usr/share/doc/openssh-5.3p1/CREDITS
/usr/share/doc/openssh-5.3p1/ChangeLog
/usr/share/doc/openssh-5.3p1/INSTALL
/usr/share/doc/openssh-5.3p1/LICENCE
/usr/share/doc/openssh-5.3p1/OVERVIEW
/usr/share/doc/openssh-5.3p1/PROTOCOL
/usr/share/doc/openssh-5.3p1/PROTOCOL.agent
/usr/share/doc/openssh-5.3p1/PROTOCOL.certkeys
/usr/share/doc/openssh-5.3p1/README
/usr/share/doc/openssh-5.3p1/README.dns
/usr/share/doc/openssh-5.3p1/README.nss
/usr/share/doc/openssh-5.3p1/README.platform
/usr/share/doc/openssh-5.3p1/README.privsep
/usr/share/doc/openssh-5.3p1/README.smartcard
/usr/share/doc/openssh-5.3p1/README.tun
/usr/share/doc/openssh-5.3p1/TODO
/usr/share/doc/openssh-5.3p1/WARNING.RNG
/usr/share/man/man1/ssh-keygen.1.gz
/usr/share/man/man8/ssh-keysign.8.gz
```

去除相关的文档和 ``man`` ，剩下没有的我们就补上。补全后问题仍然没有解决。

在小系统中输入 ``mount`` ，可以看到已经挂载的磁盘：

```shell
/dev/sda1 on / type ext4 (rw)
proc on /proc type proc (rw)
sysfs on /sys type sysfs (rw)
devpts on /dev/pts type devpts (rw,gid=5,mode=620)
tmpfs on /dev/shm type tmpfs (rw)
none on /proc/sys/fs/binfmt_misc type binfmt_misc (rw)
```

奇怪的事情来了，明明已经挂载了 ``/dev/pts`` ，但是在 ``/dev`` 下却并没有出现 ``pts`` 文件夹，推测这就是问题的根源所在！

尝试在打包前直接新建 ``/dev/pts`` 目录，但是并没有用。。。。。。最后在测试小系统 ``mkdir`` 命令时意外发现居然失效了？！重新拷贝一次 ``mkdir`` 命令，问题最终解决！

最后删去打包前新建的 ``/dev/pts`` 目录，裁剪驱动后重新打包，可以正常运行。看来就是 ``mkdir`` 命令失效带来的问题了。。。。。。挺好奇为啥会失效的，可惜目前没办法复现。

至此，已经可以实现ssh直接连接小系统了。但目前的系统缺陷如下：
* 开机不能自动配置好network和sshd，需要手动使用 ``service`` 命令实现
* 登录过程中和登陆后存在一些warning输出，虽然不影响使用，但最好还是可以解决

以上缺陷等待后续处理吧！