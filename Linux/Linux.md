

![image-20210617103727166](asset/Linux.assets/image-20210617103727166.png)

# Linux简介

​	*Linux，全称 GNU/Linux，是一种免费使用和自由传播的类 UNIX 操作系统，其内核由林纳斯·本纳第克特·托瓦兹于1991年10月5日首次发布，它主要受到 Minix 和 Unix 思想的启发，是一个基于 POSIX 的多用户、多任务、支持多线程和多CPU的操作系统。它能运行主要的 Unix 工具软件、应用程序和网络协议。它支持32位和64位硬件。Linux继承了 Unix 以网络为核心的设计思想，是一个性能稳定的多用户网络操作系统。*

​	*Linux不仅系统性能稳定，而且是开源软件。其核心防火墙组件性能高效、配置简单，保证了系统的安全。在很多企业网络中，为了追求速度和安全，Linux不仅仅是被网络运维人员当作服务器使用，甚至当作网络防火墙，这是Linux的一大亮点。*

​	*Linux的基本思想有两点：*

​	*第一，一切都是文件；*

​	*第二，每个文件都有确定的用途。*

​	*其中第一条详细来讲就是系统中的所有都归结为一个文件，包括命令、硬件和软件设备、操作系统、进程等等对于操作系统内核而言，都被视为拥有各自特性或类型的文件。至于说Linux是基于Unix的，很大程度上也是因为这两者的基本思想十分相近。*

> 服务器通常使用的两套组合是:LAMP(Linux + Apache + Mysql + PHP) 和 LNMP(Linux + Nginx + Mysql + PHP)组合。

​	

# Linux安装

常见的安装方式有三种，这里我们使用的购买的云服务器：

1. 虚拟机安装

2. 本地安装

3. 云服务器安装，这里使用的是腾讯云服务器

   

# 云服务器登录配置

1. 首先要配置防火墙

   ![image-20210617112535111](asset/Linux.assets/image-20210617112535111.png)

2. 可以修改登录密码。

3. 下载`Xshell`和`Xftp`来进行远程连接和文件传输。




# 文件系统目录结构

​	Linux一切皆文件，根目录就是 `/` 。

```shell
[root@VM-4-9-centos /]# ls
bin   data  etc   lib    lost+found  mnt  proc  run   srv  tmp  var
boot  dev   home  lib64  media       opt  root  sbin  sys  usr
```

## 一级目录

| 一级目录    | 功能（作用）                                                 |
| ----------- | :----------------------------------------------------------- |
| /bin        | 存放系统命令，普通用户和 root 都可以执行。放在 /bin 下的命令在单用户模式下也可以执行 |
| /boot       | 系统启动目录，保存与系统启动相关的文件，如内核文件和启动引导程序（grub）文件等 |
| /dev        | 设备文件保存位置                                             |
| ==/etc==    | ==配置文件保存位置。系统内所有采用默认安装方式（rpm 安装）的服务配置文件全部保存在此目录中，如用户信息、服务的启动脚本、常用服务的配置文件等== |
| ==/home==   | ==普通用户的主目录（也称为家目录）。在创建用户时，每个用户要有一个默认登录和保存自己数据的位置，就是用户的主目录，所有普通用户的主目录是在 /home/ 下建立一个和用户名相同的目录。如用户 liming 的主目录就是 /home/liming== |
| /lib        | 系统调用的函数库保存位置                                     |
| /media      | 挂载目录。系统建议用来挂载媒体设备，如软盘和光盘             |
| /mnt        | 挂载目录。早期 Linux 中只有这一个挂载目录，并没有细分。系统建议这个目录用来挂载额外的设备，如 U 盘、移动硬盘和其他操作系统的分区 |
| /misc       | 挂载目录。系统建议用来挂载 NFS 服务的共享目录。虽然系统准备了三个默认挂载目录 /media/、/mnt/、/misc/，但是到底在哪个目录中挂载什么设备可以由管理员自己决定。例如，笔者在接触 Linux 的时候，默认挂载目录只有 /mnt/，所以养成了在 /mnt/ 下建立不同目录挂载不同设备的习惯，如 /mnt/cdrom/ 挂载光盘、/mnt/usb/ 挂载 U 盘，都是可以的 |
| ==/opt==    | ==第三方安装的软件保存位置。这个目录是放置和安装其他软件的位置，手工安装的源码包软件都可以安装到这个目录中。不过笔者还是习惯把软件放到 /usr/local/ 目录中，也就是说，/usr/local/ 目录也可以用来安装软件== |
| /root       | root 的主目录。普通用户主目录在 /home/ 下，root 主目录直接在“/”下 |
| /sbin       | 保存与系统环境设置相关的命令，只有 root 可以使用这些命令进行系统环境设置，但也有些命令可以允许普通用户查看 |
| /srv        | 服务数据目录。一些系统服务启动之后，可以在这个目录中保存所需要的数据 |
| ==/tmp==    | ==临时目录。系统存放临时文件的目录，在该目录下，所有用户都可以访问和写入。建议此目录中不能保存重要数据，最好每次开机都把该目录清空== |
| /lost+found | 当系统意外崩溃或意外关机时，产生的一些文件碎片会存放在这里。在系统启动的过程中，fsck 工具会检查这里，并修复已经损坏的文件系统。这个目录只在每个分区中出现，例如，/lost+found 就是根分区的备份恢复目录，/boot/lost+found 就是 /boot 分区的备份恢复目录 |
| /proc       | 虚拟文件系统。该目录中的数据并不保存在硬盘上，而是保存到内存中。主要保存系统的内核、进程、外部设备状态和网络状态等。如 /proc/cpuinfo 是保存 CPU 信息的，/proc/devices 是保存设备驱动的列表的，/proc/filesystems 是保存文件系统列表的，/proc/net 是保存网络协议信息的...... |
| /sys        | 虚拟文件系统。和 /proc 目录相似，该目录中的数据都保存在内存中，主要保存与内核相关的信息 |
| /usr        | 全称为 Unix Software Resource，此目录用于存储系统软件资源。FHS 建议所有开发者，应把软件产品的数据合理的放置在 /usr 目录下的各子目录中，而不是为他们的产品创建单独的目录。 |
| /var        | /var 目录用于存储动态数据，例如缓存、日志文件、软件运行过程中产生的文件等。通常，此目录下含一些子目录。 |



## 重要的子目录

**Linux /usr目录**

| 子目录       | 功能（作用）                                                 |
| ------------ | ------------------------------------------------------------ |
| /usr/bin/    | 存放系统命令，普通用户和超级用户都可以执行。这些命令和系统启动无关，在单用户模式下不能执行 |
| /usr/sbin/   | 存放根文件系统不必要的系统管理命令，如多数服务程序，只有 root 可以使用。 |
| /usr/lib/    | 应用程序调用的函数库保存位置                                 |
| /usr/XllR6/  | 图形界面系统保存位置                                         |
| /usr/local/  | 手工安装的软件保存位置。我们一般建议源码包软件安装在这个位置 |
| /usr/share/  | 应用程序的资源文件保存位置，如帮助文档、说明文档和字体目录   |
| /usr/src/    | 源码包保存位置。我们手工下载的源码包和内核源码包都可以保存到这里。不过笔者更习惯把手工下载的源码包保存到 /usr/local/src/ 目录中，把内核源码保存到 /usr/src/linux/ 目录中 |
| /usr/include | C/C++ 等编程语言头文件的放置目录                             |



**Linux /var 目录**

| /var子目录        | 功能（作用）                                                 |
| ----------------- | ------------------------------------------------------------ |
| /var/lib/         | 程序运行中需要调用或改变的数据保存位置。如 [MySQL](http://c.biancheng.net/mysql/) 的数据库保存在 /var/lib/mysql/ 目录中 |
| /var/log/         | 登陆文件放置的目录，其中所包含比较重要的文件如 /var/log/messages, /var/log/wtmp 等。 |
| /var/run/         | 一些服务和程序运行后，它们的 PID（进程 ID）保存位置          |
| /var/spool/       | 里面主要都是一些临时存放，随时会被用户所调用的数据，例如 /var/spool/mail/ 存放新收到的邮件，/var/spool/cron/ 存放系统定时任务。 |
| /var/www/         | RPM 包安装的 Apache 的网页主目录                             |
| /var/nis和/var/yp | NIS 服务机制所使用的目录，nis 主要记录所有网络中每一个 client 的连接信息；yp 是 linux 的 nis 服务的日志文件存放的目录 |
| /var/tmp          | 一些应用程序在安装或执行时，需要在重启后使用的某些文件，此目录能将该类文件暂时存放起来，完成后再行删除 |

## Linux链接概念

​	Linux 链接分两种，一种被称为硬链接（Hard Link），另一种被称为符号链接（Symbolic Link）。默认情况下，**ln** 命令产生硬链接。

**硬连接**

​	硬连接指通过索引节点来进行连接。在 Linux 的文件系统中，保存在磁盘分区中的文件不管是什么类型都给它分配一个编号，称为索引节点号(Inode Index)。在 Linux 中，多个文件名指向同一索引节点是存在的。

​	比如：A 是 B 的硬链接（A 和 B 都是文件名），则 A 的目录项中的 inode 节点号与 B 的目录项中的 inode 节点号相同，即一个 inode 节点对应两个不同的文件名，两个文件名指向同一个文件，A 和 B 对文件系统来说是完全平等的。删除其中任何一个都不会影响另外一个的访问。

​	硬连接的作用是允许一个文件拥有多个有效路径名，这样用户就可以建立硬连接到重要文件，以防止“误删”的功能。其原因如上所述，因为对应该目录的索引节点有一个以上的连接。只删除一个连接并不影响索引节点本身和其它的连接，只有当最后一个连接被删除后，文件的数据块及目录的连接才会被释放。也就是说，文件真正删除的条件是与之相关的所有硬连接文件均被删除。

**软连接**

​	另外一种连接称之为符号连接（Symbolic Link），也叫软连接。软链接文件有类似于 Windows 的快捷方式。它实际上是一个特殊的文件。在符号连接中，文件实际上是一个文本文件，其中包含的有另一文件的位置信息。

​	比如：A 是 B 的软链接（A 和 B 都是文件名），A 的目录项中的 inode 节点号与 B 的目录项中的 inode 节点号不相同，A 和 B 指向的是两个不同的 inode，继而指向两块不同的数据块。但是 A 的数据块中存放的只是 B 的路径名（可以根据这个找到 B 的目录项）。A 和 B 之间是“主从”关系，如果 B 被删除了，A 仍然存在（因为两个是不同的文件），但指向的是一个无效的链接。




# 常用命令

## 开关机登录

​	Windows开机会启动很多服务 "service"，在Linux中叫做守护进程"daemon"。

​	在开关机前，最好进行内存-硬盘的数据同步，使用 `sync` 命令来进行数据同步。

```shell
sync # 将数据由内存同步到硬盘中
```

==shutdown关机==

```shell
halt				#立刻关机
poweroff			#立刻关机
shutdown -h now 	#立刻关机(root用户使用)
shutdown -h 10 		#10分钟后自动关机
```

==reboot重启==

```shell
reboot
shutdown -r now 	#立刻重启(root用户使用)
shutdown -r 10 		#过10分钟自动重启(root用户使用)
shutdown -r 20:35	#在时间为20:35时候重启(root用户使用)
```



## 目录管理

​	最常用的目录命令为:

```shell
cd .. 	#返回上一级目录
cd ./ 	#当前目录
ls 		#展示当前目录下的文件及文件夹
```



==ls列出目录==

```shell
ls -a #查看全部文件,包括隐藏文件
ls -l #查看文件详细信息
#可以组合使用
ls -al #查看全部文件,包括隐藏文件的详细信息
```



==cd切换目录==

```shell
cd /xxx/xxx # 以绝对路径进入到/xxx/xxx
cd ../xxx 	# 相对路径进入到/xxx
cd ~		#返回当前的用户目录
```



==pwd显示目录==

​	显示当前用户所在目录。

```bash
[root@VM-4-9-centos ~]# cd /
[root@VM-4-9-centos /]# pwd
/
[root@VM-4-9-centos ~]# cd ~
[root@VM-4-9-centos ~]# pwd
/root
[root@VM-4-9-centos ~]# cd /bin
[root@VM-4-9-centos bin]# pwd
/bin
[root@VM-4-9-centos bin]# cd /usr/local
[root@VM-4-9-centos local]# pwd
/usr/local
```



==mkdir创建目录==

```shell
 mkdir [-mp] 目录名称
```

选项与参数：

- -m ：配置文件的权限喔！直接配置，不需要看默认权限 (umask) 的脸色～
- -p ：帮助你直接将所需要的目录(包含上一级目录)递归创建起来！

```shell
mkdir test1 			#创建目录
mkdir -p test2/test3	 #递归创建目录
```



==rmdir删除空目录==

​	只能删除空目录。

```shell
 rmdir [-p] 目录名称 
```

选项与参数：

- -p ：连同上一级『空的』目录也一起删除



==cp复制文件或目录==

​	cp 即拷贝文件和目录。

```bash
cp [-adfilprsu] 来源档(source) 目标档(destination)
```

选项与参数：

- -a：相当於 -pdr 的意思，至於 pdr 请参考下列说明；(常用)
- -d：若来源档为连结档的属性(link file)，则复制连结档属性而非文件本身；
- -f：为强制(force)的意思，若目标文件已经存在且无法开启，则移除后再尝试一次；
- -i：若目标档(destination)已经存在时，在覆盖时会先询问动作的进行(常用)
- -l：进行硬式连结(hard link)的连结档创建，而非复制文件本身；
- -p：连同文件的属性一起复制过去，而非使用默认属性(备份常用)；
- -r：递归持续复制，用於目录的复制行为；(常用)
- -s：复制成为符号连结档 (symbolic link)，亦即『捷径』文件；
- -u：若 destination 比 source 旧才升级 destination ！

```shell
[root@VM-4-9-centos home]# ls 
kdump-lib.sh  lighthouse  test1
[root@VM-4-9-centos home]# cp kdump-lib.sh  test1/ 		#复制文件
[root@VM-4-9-centos home]# cd test1/
[root@VM-4-9-centos test1]# ls
kdump-lib.sh
[root@VM-4-9-centos home]# cp kdump-lib.sh test1/		#如果文件重复存在,会询问是否覆盖
cp: overwrite ‘test1/kdump-lib.sh’? y  
```



==rm 移除文件或目录==

```shell
 rm [-fir] 文件或目录
```

选项与参数：

- -f ：就是 force 的意思，忽略不存在的文件，不会出现警告信息；
- -i ：互动模式，在删除前会询问使用者是否动作
- -r ：递归删除啊！最常用在目录的删除了！这是非常危险的选项！！！

```shell
[root@VM-4-9-centos home]# ls
kdump-lib.sh  lighthouse  test1
[root@VM-4-9-centos home]# rm kdump-lib.sh  -i
rm: remove regular file ‘kdump-lib.sh’? y
[root@VM-4-9-centos home]# ls
lighthouse  test1
```

```bash
rm -rf /  #删除操作系统在内的所有文件!!!!!!!!!删库跑路
```



==mv(移动文件与目录,或修改名称)==

```shell
mv [-fiu] source destination
```

选项与参数：

- -f ：force 强制的意思，如果目标文件已经存在，不会询问而直接覆盖；
- -i ：若目标文件 (destination) 已经存在时，就会询问是否覆盖！
- -u ：若目标文件已经存在，且 source 比较新，才会升级 (update)

```shell
[root@VM-4-9-centos test1]# ls
kdump-lib.sh
[root@VM-4-9-centos test1]# mv kdump-lib.sh ../  #移动文件到上一级目录 
[root@VM-4-9-centos test1]# cd ..
[root@VM-4-9-centos home]# ls
kdump-lib.sh  lighthouse  test1
```

```bash
[root@VM-4-9-centos home]# mv kdump-lib.sh kdum.sh #重命名文件或文件夹
[root@VM-4-9-centos home]# ls
kdum.sh  lighthouse  test1
```



