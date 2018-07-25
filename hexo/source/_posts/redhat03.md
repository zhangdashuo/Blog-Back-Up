---
title: 小红帽第三集
no_word_count: false
date: 2018-07-20 14:03:56
tags: Linux
categories: 小红帽
---
** {{ title }}：** <Excerpt in index | 首页摘要> 
Linux的文件系统，系统管理类命令，bash基础特性
<!-- more --> 
<The rest of contents | 余下全文> 
## Linux文件系统及文件类型 

### Linux文件系统

在windows下对于硬盘的分区格式已经很了解了，一般C盘为系统盘，大约分给系统盘60G-80G即可，剩下的就是D盘E盘F盘啥的这个就根据自己的喜好和自己物理硬盘的大小进行分割就好。注意给磁盘分区后越靠近C盘的位置磁盘操作就越快，（对于磁盘的详细讲解可以关注后面的博客更新的内容），所以C盘安装系统，相应的可以把软件安装到D盘。而Linux下采用的是根文件系统（rootfs）。即所有的目录全部由根进行衍生。但这并不代表了Linux的文件系统不需要分区，对于Linux也是要分系统分区和其他数据分区的，通常内核启动之后就会寻找系统分区并将该分区挂载为根，对于其他分区的数据都是通过挂载的方式来挂载到根上，对于磁盘的操作在后期详细讲解。这里来说Linux的根下的目录结构，对Linux的文件有所了解。

对于Linux各发行版的根目录结构都很类似，他们都遵循由LSB（Linux standerd base）制定的FHS（File system hierarchy standard）标准来进行文件的规划的。对于Linux的目录结构大家可以查看该说明，进行详细的了解。以我的系统Centos来说根目录下的文件有：

```shell
[zhangshuo@localhost ~]$ ls /
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

下面来说根目录下每个文件夹所放置的内容：

/boot：Static files of the boot loader

&emsp;&emsp;引导文件存放目录，内核文件（VMlinuz），引导加载器（bootloader，grub）都存放于此文件。至于系统是如何引导启动的会在小红帽后面集中有所说明。

/bin：Essential user command binaries (for use by allusers)

&emsp;&emsp;供所有用户使用的基本二进制命令，不能关联至独立分区，该目录中存放的程序为操作系统启动就会用到的程序。

/lib：Essential shared libraries and kernel modules

&emsp;&emsp;基本共享库文件，以及内核模块文件（/lib/modules）

/sbin:System binaries

&emsp;&emsp;管理类的基本命令，不能关联至独立分区，该目录中存放的程序为操作系统启动就会用到的程序。

/lib64：Alternate（替代的） format essential shared libraries(optional)

&emsp;&emsp;专用于X86_64系统上的辅助共享库文件存放位置。

/etc：Host-specific system configuration

&emsp;&emsp;主机配置文件目录（里面的文件都为纯文本文件）

/home：User home directories (optional)

&emsp;&emsp;普通用户的家目录存放位置。

/root：Home directory for the root user (optional)

&emsp;&emsp;管理员的家目录。这里标明该文件为可选的。在一些unix系统下管理员确实是没有家目录的，因为管理员的权限太大了，所以通常管理员不会直接登陆系统，自然也就不需要家目录了。

/media：Mount point for removeable media

&emsp;&emsp;便携式移动设备的挂载点。通常我们的优盘挂载系统之后都会在该目录下。

/mnt：Mount point for a temporarily mounted filesystem

&emsp;&emsp;临时文件系统的挂载点。比如我们要把一个主机的硬盘拆下来放到另一个主机去操作通常该硬盘会挂载到该目录下。

/dev：Device files

 &emsp;&emsp;设备文件及特殊文件存放位置。来看一下该目录下的文件吧：

```shell
[zhangshuo@localhost ~]$ ls /dev/ -l
total 0
crw-------. 1 root    root     10, 235 Jul 20 22:42 autofs
crw-------. 1 root    root     10, 234 Jul 20 22:42 btrfs-control
lrwxrwxrwx. 1 root    root           3 Jul 20 22:42 cdrom -> sr0
drwxr-xr-x. 2 root    root         100 Jul 20 22:42 centos
drwxr-xr-x. 2 root    root        2820 Jul 20 22:42 char
crw-------. 1 root    root      5,   1 Jul 20 22:42 console
brw-rw----. 1 root    disk    253,   0 Jul 20 22:42 dm-0
brw-rw----. 1 root    disk    253,   1 Jul 20 22:42 dm-1
brw-rw----. 1 root    disk    253,   2 Jul 20 22:42 dm-2

```

在这些设备中的文件类型大部分为b，和c。

&emsp;&emsp;b：block device 块设备即随机访问的设备。随机访问的意思是比如在硬盘中放着123三个电影，如果要看第3个电影不需要先看1再看2才能看3，这就是随机访问。

&emsp;&emsp;c：character device 字符设备即线性访问的设备。线性访问的设备比如键盘鼠标等。如果键盘键入的值不按你敲击的顺序恐怕这个电脑离被砸已经不远了。

/opt：Add-on（附加的） application software packages

&emsp;&emsp;第三方应用程序安装位置。早些的oracle就是安装到该目录下的。

/srv：Data for services provided by this system

&emsp;&emsp;系统上运行的服务用到的数据存放位置。

/tmp：Temporary files

&emsp;&emsp;临时文件存放位置，对所有用户开放各种权限。看一下该目录的权限：

```shell
[zhangshuo@localhost ~]$ ls -ld /tmp/
drwxrwxrwt. 18 root root 4096 Jul 20 23:24 /tmp/
```

可以发现该文件的类型有个t对于t的权限会在小红帽后面讲到。

注意：以上文件除了/boot，/home之外都不可独立分区。

/usr：/usr is shareable, read-only data

&emsp;&emsp;对于/usr又是一个层级结构。/usr is the second major section of the filesystem。它是全局共享只读数据的存放目录。

&emsp;&emsp;对于该目录下的一些文件说明：

&emsp;&emsp;bin，sbin：保证系统拥有完整功能而提供的应用程序。

&emsp;&emsp;lib，lib64：为这些程序的运行提供的库文件。

&emsp;&emsp;include：Header files included by C programs

&emsp;&emsp;&emsp;&emsp;C程序的头文件所存放的位置。头文件是用来描述库文件的打开姿势，描述程序接口的调用方式，及说明要调用哪些库文件，并说明调用该库时以什么方式调用。

&emsp;&emsp;local：Local hierarchy (empty after main installation)

&emsp;&emsp;&emsp;&emsp;第三方程序的安装目录，我的博客系统就是安装在该目录下的。这个目录下又是一个独立的层级结构，在该目录下也会有：bin，sbin，lib，lib64，etc，share

/var：contains variable data files.经常变化的数据存放位置。

&emsp;&emsp;cache：Application cache data 应用程序缓存数据目录。

&emsp;&emsp;lib：Variable state information 应用程序状态数据信息。

&emsp;&emsp;local：Variable data for /usr/local 专用于为/usr/local下的应用程序存储可变数据。

&emsp;&emsp;lock：Lock files 锁文件存放目录。

&emsp;&emsp;log：Log files and directories 日志文件和目录

&emsp;&emsp;opt：Variable data for /opt 专用于为/opt下的应用程序存储可变数据。

&emsp;&emsp;run：Data relevant（有关的） to running processes 运行中的进程相关的数据，通常用于存储进程的PID文件。

&emsp;&emsp;spool：Application spool data 应用数据的缓冲池。

&emsp;&emsp;tmp：Temporary files preserved between system reboots 保存系统两次重启之间产生的临时数据。

/proc：Kernel and process information virtual filesystem 用于输出内核于进程信息相关的虚拟文件系统。

/sys：用于输出当前系统上硬件设备相关信息的虚拟文件系统。例如块设备，蓝牙设备，网络设备等。

### Linux上的应用程序的组成部分

Linux下每个安装好的程序都应该有如下部分：

&emsp;&emsp;二进制程序：/bin，/sbin，/usr/bin，/usr/sbin，/usr/local/bin，/usr/local/sbin等目录下的可执行程序。

&emsp;&emsp;库文件：/lib，/lib64，/usr/lib，/usr/lib64，/usr/local/lib，/usr/local/lib64等目录下。

&emsp;&emsp;配置文件：/etc，/etc/DIRECTORY，/usr/local/etc等目录下。

&emsp;&emsp;帮助文件：/usr/local/man，/usr/local/doc，/usr/local/share/man，/usr/local/share/doc等目录下。

### Linux下的文件类型

使用`ls -l`命令可以查看该文件的文件类型。Linux的文件类型有如下几种：

&emsp;&emsp;-(f)：普通文件

&emsp;&emsp;d：目录文件

&emsp;&emsp;b：块设备文件，这类文件只有元数据而没有真正的数据，因为他们只是设备的访问入口而已。

&emsp;&emsp;c：字符设备，和块设备的属性类似。

&emsp;&emsp;l：符号链接文件

&emsp;&emsp;p：管道文件：FIFO

&emsp;&emsp;s：套接字文件

## bash基础特性及基础命令

### 目录管理类命令

命令：cd，pwd，ls，mkdir，rmdir，tree。

mkdir [OPTION]... DIRECTORY... ：make directories 创建目录

&emsp;&emsp;-p, --parents：该目录名再其上级目录中存在，则创建时不报错，且可自动创建所需的各目录。

&emsp;&emsp;-v, --verbose：显示详细信息。

&emsp;&emsp;-m, --mode=MODE：创建目录时同时指定其权限。

rmdir [OPTION]... DIRECTORY...：remove empty directories 删除空目录

&emsp;&emsp;-v, --verbose：显示详细过程。

&emsp;&emsp;-p, --parents：递归删除。这个命令基本很少用。看一个例子吧：

```shell
[zhangshuo@localhost tmp]$ tree x #显示目录树状结构
x
└── y
    └── z
        └── a

3 directories, 0 files
[zhangshuo@localhost tmp]$ rmdir -p x/y/z/a #使用-p选项进行递归删除
[zhangshuo@localhost tmp]$ ls -ld x	#因为a目录为空，所以删除，删除之后发现z目录为空所以也删除，同样所以x也被删除
ls: cannot access x: No such file or directory
```

tree [OPTION]... DIRECTORY... ：显示目录的树状结构，它的选项很多一般我们只会用到下面几个：

&emsp;&emsp;-d：只显示目录。

&emsp;&emsp;-L：限制层级数目。

&emsp;&emsp;-P pattern：只显示由指定pattern匹配到的路径。

### 文本文件查看类命令补充

命令：cat，tac，more，less，tail，head

more [options] file [...]：more查看文本在翻到最后面时直接退出。

&emsp;&emsp;-d：显示翻页及退出提示。

less [options] file [...]：和man的操作类似，因为man对于帮助文档的显示就是调用的less命令。

head [options] file [...]：显示文章的首部，默认为显示文件的前10行。

&emsp;&emsp;-c #：指定获取前#字节。

&emsp;&emsp;-n #：指定获取前#行。

&emsp;&emsp;-#：指定获取前#行。

tail [options] file [...]：显示文章的尾部，默认为显示文件的后10行。

&emsp;&emsp;-c #：指定获取后#字节。

&emsp;&emsp;-n #：指定获取后#行。

&emsp;&emsp;-#：指定获取后#行。

&emsp;&emsp;-f：跟踪显示文件新追加的内容，通常对于日志文件的产看对于该选项使用较多。

### 文件的时间戳管理工具

命令：touch

在上集的文件系统部分对于文件的数据有所描述，即对于Linux下的每个文件都由metadata（源数据），data（数据组成）。对于文件的状态可以使用stat命令进行查看。

stat [OPTION]... FILE...：display file or file system status 显示文件的状态信息。

```shell
[zhangshuo@localhost hexo]$ stat _config.yml #stat命令来查看_config.yml配置文件的信息
  File: ‘_config.yml’	#文件名
  Size: 2512（大小）    Blocks: 8（磁盘块大小）   IO Block: 4096（IO块大小）  regular file
Device: fd02h/64770d	Inode: 134279906（inode号）   Links: 1（文件被硬链接的次数）
Access: (0664/-rw-rw-r--)  Uid: ( 1000/zhangshuo)（属主）   Gid: ( 1000/zhangshuo)（属组）
Context: unconfined_u:object_r:user_home_t:s0
Access: 2018-07-20 13:11:22.033729289 +0800（访问时间）
Modify: 2018-07-20 13:11:18.115237469 +0800（修改时间）
Change: 2018-07-20 13:11:18.124987656 +0800（改变时间）
 Birth: -
```

对于Linux下每个文件都有三个时间戳：

&emsp;&emsp;Access time：访问时间，简写为atime  表示文件最近一次被读取的时间。

&emsp;&emsp;Modify time：修改时间，简写为mtime 表示文件最近一次被修改的时间。这里修改的内容为文件的数据内容。

&emsp;&emsp;Change time：改变时间，简写为ctime 表示文件最近一次源数据发生改变的时间。只要文件的atime或是mtime发生改变则ctime比发生改变。

touch [OPTION]... FILE...：change file timestamps touch命令用于改变文件的时间戳。

&emsp;&emsp;-a：只修改访问时间

&emsp;&emsp;-m：只修改修改时间

&emsp;&emsp;-t STAMP：

&emsp;&emsp;&emsp;&emsp;use [[CC]YY]MMDDhhmm[.ss] instead of current time：修改为指定的时间。

&emsp;&emsp;-c：如果文件不存在，则不予创建。

### linux的bash基础特性

1. 命令历史

   命令：`history`对于该命令相关的所包含的环境变量有如下几个：

&emsp;&emsp;&emsp;&emsp;HISTSIZE：定义命令历史记录的条数。

&emsp;&emsp;&emsp;&emsp;HISTFILE：定义命令历史所保存的文件。通常文件为：~/.bash_history

&emsp;&emsp;&emsp;&emsp;HISTFILESIZE：命令历史文件记录历史的条数。

&emsp;&emsp;&emsp;&emsp;HISTCONTROL：命令历史的记录方式。

&emsp;&emsp;history：Display or manipulate（操作） the history list.

&emsp;&emsp;用法：`history [-c][-d offset] [n] or history -anrw [filename] or history -ps arg [arg...]`

&emsp;&emsp;&emsp;&emsp;-d OFFSET：删除某条命令历史。

&emsp;&emsp;&emsp;&emsp;-c：清空整个命令历史。

&emsp;&emsp;&emsp;&emsp;#：显示历史中最近的#条命令。

&emsp;&emsp;&emsp;&emsp;-a：手动追加当前会话缓冲区的命令历史至历史文件中。

&emsp;&emsp;调用命令历史中的命令：

&emsp;&emsp;&emsp;&emsp;!#：重复执行第#条指令。

&emsp;&emsp;&emsp;&emsp;!!：重复执行上一条命令。

&emsp;&emsp;&emsp;&emsp;!string：最近一次以string开头的指令。

&emsp;&emsp;调用上一条命令的最后一个参数：

&emsp;&emsp;&emsp;&emsp;!$：

&emsp;&emsp;&emsp;&emsp;ESC，.：按一下ESC松开然后再按.

&emsp;&emsp;&emsp;&emsp;ALT+.：按着ALT不松开，然后按.

&emsp;&emsp;控制命令历史的记录方式：

&emsp;&emsp;&emsp;&emsp;环境变量：HISTCONTROL

&emsp;&emsp;&emsp;&emsp;它的取值所表示的意义：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;ignoredups：忽略重复的命令（连续且完全相同的方为重复）。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;ignorespace：忽略空格开头的命令。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;ignoreboth：上述都生效。

&emsp;&emsp;&emsp;&emsp;更改环境变量的值的方式：`export HISTCONTROL='ignoreboth'`

2. 命令补全

   bash执行命令的过程上一级中有所说明这里再提一下：

&emsp;&emsp;&emsp;&emsp;对于内部命令：直接调用运行

&emsp;&emsp;&emsp;&emsp;对于外部命令：需要在PATH环境变量中规定的路径从左至右进行查找，第一次找到的即为要执行的命令。所以命令补全的机制也是基于此。

&emsp;&emsp;&emsp;&emsp;直接补全：按TAB键，用户给定的字符串只有一条唯一对应的命令，以用户给定的字符串为开头。如果对应的命令不唯一，则再次按TAB键可以列表的形式给出。

3. 路径补全

   把用户给出的字符串当作路径开关，并在其指定上级目录下搜索以指定的字符串开头的文件名。用法和命令补全类似。

   建议：对于命令补全和路径补全建议经常使用。这样不仅可以提高效率而且可以保证命令执行的准确性。

4. 命令行展开

   上集说到一个命令行展开那就是"~",它表示用户的主目录。这里再进行总结补充：

&emsp;&emsp;&emsp;&emsp;~：展开为用户的主目录。

&emsp;&emsp;&emsp;&emsp;~ USERNAME：展开为指定用户的主目录。

&emsp;&emsp;&emsp;&emsp;{}：可承载一个以逗号分隔的列表，并将其展开为多个路径，比如：/tmp/{a,b} 则展开为：/tmp/a，/tmp/b

   对于花括号展开的举例如下：

   ```shell
   [zhangshuo@localhost test]$ ls #test目录下为空
   #创建 x/y1，x/y2，x/y1/a，x/y1/b，x/y2/a，x/y2/b
   [zhangshuo@localhost test]$ mkdir -p x/{y1,y2}/{a,b}
   [zhangshuo@localhost test]$ tree x
   x
   ├── y1
   │   ├── a
   │   └── b
   └── y2
       ├── a
       └── b
   6 directories, 0 files
   #创建ab_ef，ab_gh，cd_ef，cd_gh四个文件夹
   [zhangshuo@localhost test]$ mkdir -p {ab,cd}_{ef,gh}
   [zhangshuo@localhost test]$ ls
   ab_ef  ab_gh  cd_ef  cd_gh  x
   #创建bin，sbin，usr，usr/bin，usr/sbin。
   [zhangshuo@localhost test]$ mkdir -p {bin,sbin,usr/{bin,sbin}}
   #最终的结果如下
   [zhangshuo@localhost test]$ tree ../test/
   ../test/
   ├── ab_ef
   ├── ab_gh
   ├── bin
   ├── cd_ef
   ├── cd_gh
   ├── sbin
   ├── usr
   │   ├── bin
   │   └── sbin
   └── x
       ├── y1
       │   ├── a
       │   └── b
       └── y2
           ├── a
           └── b
   16 directories, 0 files
   ```

   

5. 命令的执行结果状态

   命令的执行结果状态有两种：

&emsp;&emsp;&emsp;&emsp;要么成功：

&emsp;&emsp;&emsp;&emsp;要么失败：

&emsp;&emsp;在bash中，使用特殊变量$?保存最近一条命令的执行状态结果。

&emsp;&emsp;&emsp;&emsp;0：表示成功过

&emsp;&emsp;&emsp;&emsp;1-255：都表示失败

&emsp;&emsp;程序的执行有两类结果：

&emsp;&emsp;&emsp;&emsp;一种程序的返回值：

&emsp;&emsp;&emsp;&emsp;一种程序的执行状态结果：

&emsp;&emsp;&emsp;&emsp;比如：

   ```shell
   [zhangshuo@localhost test]$ ls /var #程序正常运行
   account  cache  db     ftp    gopher    lib    lock  mail  opt       run    target  yp
   adm      crash  empty  games  kerberos  local  log   nis   preserve  spool  tmp
   [zhangshuo@localhost test]$ echo $? #执行状态返回结果
   0 #表示成功
   [zhangshuo@localhost test]$ lls /var #命令书写错误
   bash: lls: command not found...
   Similar command is: 'ls'
   [zhangshuo@localhost test]$ echo $?
   127 #状态返回结果不为0表示失败
   [zhangshuo@localhost test]$ ls /varrr
   ls: cannot access /varrr: No such file or directory
   [zhangshuo@localhost test]$ echo $?
   2
   ```

-EOF
