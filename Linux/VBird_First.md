# 鸟哥的Linux私房菜基础篇_上 -- 文件系统、权限、账号、进程管理

<!-- toc -->

## 第六章、Linux 的文件权限与目录配置

#### [目录与文件之权限意义](http://vbird.dic.ksu.edu.tw/linux_basic/0210filepermission_2.php#filepermission_dir)

对于文件的rwx来说， 主要都是针对『文件的内容』而言，与文件档名的存在与否没有关系喔！因为文件记录的是实际的数据嘛！

| operation | for file | for directory |
| ----------|----------|---------------|
| r |可读取此一文件的实际内容，如读取文本文件的文字内容等|表示具有读取目录结构列表的权限，可用ls, 如果没有x权限，无法到该目录下读取文件(最多只能看到檔名)|
| w |可以编辑、新增或者是修改该文件的内容(但不含删除该文件)|具有异动该目录结构列表的权限(与该目录底下的文件名异动有关): <br/><br/>建立新的文件与目录；<br/>删除已经存在的文件与目录(不论该文件的权限为何！)；<br/>将已存在的文件或目录进行更名；<br/>搬移该目录内的文件、目录位置 |
| x |该文件具有可以被系统执行的权限| 用户能否进入该目录成为工作目, 可用cd <br/><br/>如果你在某目录下不具有x的权限， 那么你就无法切换到该目录下，也就无法执行该目录下的任何指令|

要注意：要开放目录给任何人浏览时，应该至少也要给予r及x的权限，但w权限不可随便给！

文件种类：  
正规文件(-): 纯文本档(ASCII),二进制文件(binary),数据格式文件(data)  
目录(d), 连结档(l), 区块(block)设备档(b), 字符(character)设备文件(c), 数据接口文件(sockets)(s), 数据输送文件(FIFO, pipe)(p)

## 第七章、Linux 文件与目录管理

#### Linux File System

我们可以称呼一个可被挂载的数据为一个文件系统而不是一个分割槽(partition)。  

Linux的文件系统通常会将文件数据分别存放在不同的区块，**权限与属性**放置到 inode 中，至于**实际数据**则放置到 data block 区块中。 另外，还有一个超级区块 (superblock) 会记录整个文件系统的整体信息，包括 inode 与 block 的总量、使用量、剩余量等。

每个 inode 与 block 都有编号，至于这三个数据的意义可以简略说明如下：

- superblock：记录此 filesystem 的整体信息，包括inode/block的总量、使用量、剩余量， 以及文件系统的格式与相关信息等；
- inode：记录文件的属性，一个文件占用一个inode，同时记录此文件的数据所在的 block 号码；
- block：实际记录文件的内容，若文件太大时，会占用多个 block 。

比如 inode 记录了文件数据的实际放置点为 2, 7, 13, 15， 这种数据存取的方法(Ext2)我们称为**索引式文件系统**(indexed allocation)，随机读取。

闪盘(闪存)的文件系统一般为 FAT 格式(没有 inode), 只能一个一个的按照写入顺序 2->7->13->15 将这四个 block 顺序读出

由于文件写入的 block 太过于离散了，文件读取很差，这个时候可以透过碎片整理将同一个文件所属的 blocks 汇整在一起，这样数据的读取会比较容易。FAT 需要经常的碎片整理一下，Ext2 基本上不太需要。

![](/images/ext2_filesystem.jpg)  

因为 inode 与 block 的数量太庞大, 不容易管理, 因此 Ext2 文件系统在格式化的时候基本上是区分为多个区块群组 (block group) 的，每个区块群组都有独立的 inode/block/superblock 系统

文件系统最前面有一个**启动扇区**(boot sector)，这个启动扇区可以安装启动管理程序

inode 记录的文件数据至少有底下这些：(注4)

```
该文件的存取模式(read/write/excute)；
该文件的拥有者与群组(owner/group)；
该文件的容量；
该文件创建或状态改变的时间(ctime)；
最近一次的读取时间(atime)；
最近修改的时间(mtime)；
定义文件特性的旗标(flag)，如 SetUID...；
该文件真正内容的指向 (pointer)；
```

inode 和 block 的数量与大小也是在格式化时就已经固定了，除此之外 inode 还有些什么特色呢？

```
每个 inode 大小均固定为 128 bytes；
每个文件都仅会占用一个 inode 而已；
承上，因此文件系统能够创建的文件数量与 inode 的数量有关；
系统读取文件时需要先找到 inode，并分析 inode 所记录的权限与用户是否符合，若符合才能够开始实际读取 block 的内容。
```

Superblock (1024bytes) 是记录整个 filesystem 相关信息的地方， 没有 Superblock ，就没有这个 filesystem 了。他记录的信息主要有：

```
block 与 inode 的总量；
未使用与已使用的 inode / block 数量；
block 与 inode 的大小 (block 为 1, 2, 4K，inode 为 128 bytes)；
filesystem 的挂载时间、最近一次写入数据的时间、最近一次检验磁盘 (fsck) 的时间等文件系统的相关信息；
一个 valid bit 数值，若此文件系统已被挂载，则 valid bit 为 0 ，若未被挂载，则 valid bit 为 1 。
```

相关的 superblock 信息我们等一下会以 **dumpe2fs** 命令来呼叫出来观察喔！

每个 block group 都可能含有 superblock 喔！但是一个文件系统应该仅有一个 superblock，怎么回事啊？
> 事实上除了第一个 block group 内会含有 superblock 之外，后续的 block group 不一定含有 superblock ， 而若含有 superblock 则该 superblock 主要是做为第一个 block group 内 superblock 的备份咯，这样可以进行 superblock 的救援!

Filesystem Description (文件系统描述说明) 可以描述每个 block group 的开始与结束的 block 号码，以及说明每个区段 (superblock, bitmap, inodemap, data block) 分别介于哪一个 block 号码之间。这部份也能够用 **dumpe2fs** 来观察的。

```
[root@www ~]# df   <==这个命令可以叫出目前挂载的装置
Filesystem    1K-blocks      Used Available Use% Mounted on
/dev/hdc2       9920624   3822848   5585708  41% /     
/dev/hdc1       4956316    141376   4559108   4% /home

[root@www ~]# dumpe2fs [-bh] 装置文件名
选项与参数：
-b ：列出保留为坏轨的部分(一般用不到吧！？)
-h ：仅列出 superblock 的数据，不会列出其他的区段内容！

[root@www ~]# dumpe2fs /dev/hdc2
dumpe2fs 1.39 (29-May-2006)
Filesystem volume name:   /1             <==这个是文件系统的名称(Label)
Filesystem features:      has_journal ext_attr resize_inode dir_index 
  filetype needs_recovery sparse_super large_file
Default mount options:    user_xattr acl <==默认挂载的参数
Filesystem state:         clean          <==这个文件系统是没问题的(clean)
Errors behavior:          Continue
Filesystem OS type:       Linux
Inode count:              2560864        <==inode的总数
Block count:              2560359        <==block的总数
Free blocks:              1524760        <==还有多少个 block 可用
Free inodes:              2411225        <==还有多少个 inode 可用
First block:              0
Block size:               4096           <==每个 block 的大小啦！
Filesystem created:       Fri Sep  5 01:49:20 2008
Last mount time:          Mon Sep 22 12:09:30 2008
Last write time:          Mon Sep 22 12:09:30 2008
Last checked:             Fri Sep  5 01:49:20 2008
First inode:              11
Inode size:               128            <==每个 inode 的大小
Journal inode:            8              <==底下这三个与下一小节有关
Journal backup:           inode blocks
Journal size:             128M

Group 0: (Blocks 0-32767) <==第一个 block group 内容, 包含 block 的启始/结束号码
  Primary superblock at 0, Group descriptors at 1-1  <==超级区块在 0 号 block
  Reserved GDT blocks at 2-626
  Block bitmap at 627 (+627), Inode bitmap at 628 (+628)
  Inode table at 629-1641 (+629)                     <==inode table 所在的 block
  0 free blocks, 32405 free inodes, 2 directories    <==所有 block 都用完了！
  Free blocks:
  Free inodes: 12-32416                              <==剩余未使用的 inode 号码
Group 1: (Blocks 32768-65535)
....(底下省略)....
# 由于数据量非常的庞大，因此鸟哥将一些信息省略输出了！上表与你的屏幕会有点差异。
# 前半部在秀出 supberblock 的内容，包括标头名称(Label)以及inode/block的相关信息
# 后面则是每个 block group 的个别信息了！您可以看到各区段数据所在的号码！
# 也就是说，基本上所有的数据还是与 block 的号码有关就是了！很重要！
```

`ll -di / /etc /etc/passwd` 的读取流程为(假设读取者身份为 vbird):

1. / 的 inode：**透过挂载点的信息**找到 /dev/hdc2 的 inode 号码为 2 的根目录 inode，且 inode 规范的权限让我们可以读取该 block 的内容(有 r 与 x) ；
2. / 的 block：经过上个步骤取得 block 的号码，并找到该内容有 etc/ 目录的 inode 号码 (1912545)； 
3. etc/ 的 inode：读取 1912545 号 inode 得知 vbird 具有 r 与 x 的权限，因此可以读取 etc/ 的 block 内容； 
4. etc/ 的 block：经过上个步骤取得 block 号码，并找到该内容有 passwd 文件的 inode 号码 (1914888)； 
5. passwd 的 inode：读取 1914888 号 inode 得知 vbird 具有 r 的权限，因此可以读取 passwd 的 block 内容； 
6. passwd 的 block：最后将该 block 内容的数据读出来。

新建一个文件或目录:

1. 先确定用户对于欲新增文件的目录是否具有 w 与 x 的权限，若有的话才能新增；
2. 根据 inode bitmap 找到没有使用的 inode 号码，并将新文件的权限/属性写入；
3. 根据 block bitmap 找到没有使用中的 block 号码，并将实际的数据写入 block 中，且升级 inode 的 block 指向数据；
4. 将刚刚写入的 inode 与 block 数据同步升级 inode bitmap 与 block bitmap，并升级 superblock 的内容。

一般来说，我们将 inode table 与 data block 称为数据存放区域，至于其他例如 superblock、 block bitmap 与 inode bitmap 等区段就被称为 metadata (中介数据) 啰，因为 superblock, inode bitmap 及 block bitmap 的数据是经常变动的，每次新增、移除、编辑时都可能会影响到这三个部分的数据，因此才被称为中介数据的啦

>挂载点(mount point): 

每个 filesystem 都有独立的 inode / block / superblock 等信息，这个文件系统要能够链接到目录树才能被我们使用。 将文件系统与目录树结合的动作我们称为『挂载』。

挂载点一定是目录，该目录为进入该文件系统的入口。

>Linux VFS (Virtual Filesystem Switch)：

Linux 都是透过 VFS核心功能去读取 filesystem  

![](http://linux.vbird.org/linux_basic/0230filesystem/centos7_vfs.gif)

- 解析Linux中的VFS文件系统机制: http://www.ibm.com/developerworks/cn/linux/l-vfs/index.html
- 硬盘的读写原理: (http://blog.csdn.net/hguisu/article/details/7408047)

Linux File

- 非纯文字档查看命令： od
- cp 会复制执行者的属性与权限

#### [文件与目录的默认权限与隐藏权限](http://vbird.dic.ksu.edu.tw/linux_basic/0220filemanager_4.php)  

umask [-S] 指定 『目前使用者在创建文件或目录时候的权限默认值』,umask 的分数指的是『该默认值需要减掉的权限！』: 文件默认666 目录默认777   
暂时性更改umask：umask=027 (umask 027). 
umask文件保存与/etc/bashrc中 想要永久生效可以更改/etc/bashrc/umask文件,想要只对自己生效可以更改家目录下的.bashrc文件 添加一行umask=nnn即可  

The minimum and maximum UMASK value for a folder is 000 and 777  
The minimum and maximum UMASK value for a file is 000 and 666 


- chattr, lsattr 配置和查看r、w、x之外的系统隐藏属性
- file: 查看文件类型，查看某个文件的基本数据，例如是属於 ASCII 或者是 data 文件，或者是 binary ， 且其中有没有使用到动态函式库 (share library) 等等

**命令与文件的搜寻**

which: 根据『PATH』这个环境变量所规范的路径，去搜寻命令的档名  
whereis: 速度会比 find 快  
updatedb: 根据 /etc/updatedb.conf 的配置去搜寻系统硬盘内的档名，并升级 /var/lib/mlocate 内的数据库文件  
locate: 依据 /var/lib/mlocate 内的数据库记载，找出使用者输入的关键字档名  
find: find / -param1 value1 ... -paramx valuex -exec ls -l {} \; 
find / -name filename -type d -perm [+-]7000

因为 Linux 系统会将系统内的所有文件都记录在一个数据库文件里面， 而当使用 whereis 或者locate 时，都会以此数据库文件的内容为准， 因此，有的时后你还会发现使用这两个运行档时，会找到已经被杀掉的文件！ 而且也找不到最新的刚刚创建的文件呢！这就是因为这两个命令是由数据库当中的结果去搜寻文件的所在啊！

**极重要！权限与命令间的关系**

一、让使用者能进入某目录成为『可工作目录』的基本权限为何？  
可使用的命令：例如 cd 等变换工作目录的命令；  
目录所需权限：使用者对这个目录至少需要具有 x 的权限  
额外需求：如果使用者想要在这个目录内利用 ls 查阅档名，则使用者对此目录还需要 r 的权限。

二、使用者在某个目录内读取一个文件的基本权限为何？  
可使用的命令：例如本章谈到的 cat, more, less等等  
目录所需权限：使用者对这个目录至少需要具有 x 权限；  
文件所需权限：使用者对文件至少需要具有 r 的权限才行！  

三、让使用者可以修改一个文件的基本权限为何？  
可使用的命令：例如 nano 或未来要介绍的 vi 编辑器等；   
目录所需权限：使用者在该文件所在的目录至少要有 x 权限；  
文件所需权限：使用者对该文件至少要有 r, w 权限

四、让一个使用者可以创建一个文件的基本权限为何？  
目录所需权限：使用者在该目录要具有 w,x 的权限，重点在 w 啦！
 
五、让使用者进入某目录并运行该目录下的某个命令之基本权限为何？  
目录所需权限：使用者在该目录至少要有 x 的权限；  
文件所需权限：使用者在该文件至少需要有 x 的权限

#### [文件特殊权限： SUID(s), SGID(s), SBIT(t)](http://vbird.dic.ksu.edu.tw/linux_basic/0220filemanager_4.php#suid_sgid_sbit)

**SUID**: 当`s`这个标志出现在文件拥有者的`x`权限上时,称为 `Set UID`,即`SUID`的特殊权限

- SUID 权限仅对二进位程序(binary program)且仅在运行该程序的过程中(run-time)有效；
- 运行者对於该程序需要具有`x`的可运行权限；
- 运行者将具有该程序拥有者 (owner) 的权限。

你在触发 passwd 后，会取得一个新的程序与 PID，该 PID 产生时透过 SUID 来给予该 PID 特殊的权限配置, 即：SUID程序运行过程中产生的程序。

**SGID**: 当`s`标志在群组的`x`权限上时为`Set GID`

SGID用在二进位程序上时，如果程序运行者对於该程序需具备`x`的权限,则
运行者在运行的过程中将会获得该程序群组的支持！ 
 
SGID用在目录上时，使用者若对於此目录具有`r`与`x`的权限时，则  
 
- 该使用者能够进入此目录  
- 使用者在此目录下的有效群组(effective group)将会变成该目录的群组（
用途：若使用者在此目录下具有`w`的权限(可以新建文件)，则使用者所创建的新文件的群组与此目录的群组相同）。

**SBIT**: 当`s`标志出现在其他人`x`权限上时为`Sticky Bit`  

- SBIT 权限仅对目录有效
- 当使用者对於此目录具有 `w`, `x` 权限，亦即具有写入的权限时；
- 使用者在该目录下创建的文件或目录，仅有自己与root才有权力删除

如果将A目录加上了SBIT的权限时，而甲对于A目录是具有群组或其他人的身份，则甲只能够针对自己创建的文件或目录进行删除/更名/移动等动作，而无法删除他人的文件。

**SUID/SGID/SBIT 权限配置** 

SUID=4, SGID=2, SBIT=1 : `chmod 4*** file, chmod u=rwxs,go=x file `   
`S`, `T` 代表没有`x`权限， `s`, `t`代表有`x`权限 

## 第十四章、Linux 账号管理与 ACL 权限配置

**登录**

Linux 系统上面的用户如果需要登陆主机以取得 shell 的环境来工作时，他需要如何进行呢？  
首先，他必须要在计算机前面利用 tty1~tty7 的终端机提供的login接口，并输入账号与口令后才能够登陆，如果是透过网络的话，可以使用ssh。那么你输入账号口令后，系统帮你处理了什么呢？   

- 先找寻 /etc/passwd 里面是否有你输入的账号？如果没有则跳出，如果有的话则将该账号对应的 UID 与 GID (在 /etc/group 中) 读出来，另外，该账号的家目录与 shell 配置也一并读出；
- 再来则是核对口令表啦！这时 Linux 会进入 /etc/shadow 里面找出对应的账号与 UID，然后核对一下你刚刚输入的口令与里头的口令是否相符？
- 如果一切都 OK 的话，就进入 Shell 控管的阶段啰！

/etc/passwd:  
root:*:0:0:System Administrator:/var/root:/bin/sh  
账号名称:密码:UID:GID 初始群组(initial group):用户信息说明:家目录:Shell

/etc/group  
daemon:*:1:root  
组名:密码:GID:此群组支持的账号名称 

/etc/shadow(Note: there is no "/etc/shadow" on Mac)  
daemon:*:14126:0:99999:7:::
账号名称:密码:最近更动口令的日期:口令不可被更动的天数:口令需要重新变更的天数:口令需要变更期限前的警告天数(与第 5 字段相比):口令过期后的账号宽限时间(口令失效日)(与第 5 字段相比):账号失效天数:保留

/etc/gshadow  
daemon:::root,bin,daemon  
组名:口令栏:群组管理员的账号:该群组的所属账号

groups: 查看有效与支持群组, 第一个输出的群组即为有效群组 (effective group)  
newgrp: 有效群组的切换, 启用新shell登录环境，用户的环境配置(例如环境变量等等其他数据)不会有影响，但是使用者的『群组权限』将会重新被计算

加入一个群组有两个方式，一个是透过系统管理员 (root) 利用 `usermod` 帮你加入，如果 root 太忙了而且你的系统有配置群组管理员，那么你可以透过群组管理员以 `gpasswd` 帮你加入他所管理的群组中

**忘记密码**

一般用户的口令忘记了：这个最容易解决，请系统管理员帮忙， 他会重新配置好你的口令而不需要知道你的旧口令！利用 root 的身份使用 passwd 命令来处理即可。

root 口令忘记了：这就麻烦了！因为你无法使用 root 的身份登陆了嘛！ 但我们知道 root 的口令在 /etc/shadow 当中，因此你可以使用各种可行的方法启动进入 Linux 再去修改。 例如重新启动进入单人维护模式(第二十章)后，系统会主动的给予 root 权限的 bash 接口， 此时再以 passwd 修改口令即可；或以 Live CD 启动后挂载根目录去修改 /etc/shadow，将里面的 root 的口令字段清空， 再重新启动后 root 将不用口令即可登陆！登陆后再赶快以 passwd 命令去配置 root 口令即可

**账号管理**  

1. 新增与移除使用者： useradd, useradd 参考档, passwd, chage, usermod, userdel  
2. 用户功能：finger, chfn, chsh, id  
3. 新增与移除群组：groupadd, groupmod, groupdel, gpasswd 群组管理员

**su** 身份切换命令,总结用法：

- 若要完整的切换到新使用者的环境，必须要使用『 su - username 』或『 su -l username 』，才会连同 PATH/USER/MAIL 等变量都转成新用户的环境；
- 如果仅想要运行一次 root 的命令，可以利用『 su - -c "命令串" 』的方式来处理；
- 使用 root 切换成为任何使用者时，并不需要输入新用户的口令；　

**sudo**　 sudo 可以让你以其他用户的身份运行命令 (通常是使用 root 的身份来运行命令)，因此并非所有人都能够运行 sudo ， 而是仅有规范到 /etc/sudoers 内的用户才能够运行 sudo 这个命令

sudo 默认仅有 root 能使用啊！为什么呢？因为 sudo 的运行是这样的流程：

- 当用户运行 sudo 时，系统于 /etc/sudoers 文件中搜寻该使用者是否有运行 sudo 的权限；
- 若使用者具有可运行 sudo 的权限后，便让使用者『输入用户自己的口令』来确认；
- 若口令输入成功，便开始进行 sudo 后续接的命令(但 root 运行 sudo 时，不需要输入口令)；
- 若欲切换的身份与运行者身份相同，那也不需要输入口令。

所以说，sudo 运行的重点是：『能否使用 sudo 必须要看 /etc/sudoers 的配置值， 而可使用 sudo 者是透过输入用户自己的口令来运行后续的命令串』喔！ 请参考：**[/etc/sudoers文件的编辑](http://vbird.dic.ksu.edu.tw/linux_basic/0410accountmanager_4.php#visudo)**

**特殊的shell**：/sbin/nologin

所谓的『无法登陆』指的仅是：『这个使用者无法使用 bash 或其他 shell 来登陆系统』而已， 并不是说这个账号就无法使用其他的系统资源喔！ 举例来说，各个系统账号，打印作业由 lp 这个账号在管理， WWW 服务由 apache 这个账号在管理， 他们都可以进行系统程序的工作，但是『就是无法登陆主机』而已。

如果我想要让某个具有 /sbin/nologin 的使用者知道，他们不能登陆主机时， 其实我可以创建『 /etc/nologin.txt 』这个文件， 并且在这个文件内说明不能登陆的原因，那么下次当这个用户想要登陆系统时， 屏幕上出现的就会是 /etc/nologin.txt 这个文件的内容，而不是默认的内容

查询一个用户的相关数据： id, finger 等命令  
查询用户什么时候登陆： w, who, last, lastlog 等命令  
用户之间交谈：write user2 pts/2  
不想要接受任何信息: mesg n  
使用邮箱：mail username@localhost -s "邮件标题" < filename 来将文件内容传输即可  
收信：mail  
mail 请参考：http://vbird.dic.ksu.edu.tw/linux_basic/0410accountmanager_6.php#mail

---
## 第十六章、例行性工作排程

Linux 工作排程的种类： at, crontab

一种是例行性的，就是每隔一定的周期要来办的事项；  
一种是突发性的，就是这次做完以后就没有的那一种 (计算机大降价...)  

**at** ：at 是个可以处理仅运行一次就结束排程的命令，不过要运行 at 时， 必须要有 atd 这个服务 (第十八章) 的支持才行。在某些新版的 distributions 中，atd 可能默认并没有启动，那么 at 这个命令就会失效呢！不过 CentOS 默认是启动的！  
**crontab** ：crontab 这个命令所配置的工作将会循环的一直进行下去！ 可循环的时间为分钟、小时、每周、每月或每年等。crontab 除了可以使用命令运行外，亦可编辑 /etc/crontab 来支持。 至於让 crontab 可以生效的服务则是 crond 这个服务！

**atd** : 要使用单一工作排程时，我们的 Linux 系统上面必须要有负责这个排程的服务，那就是 atd 这个玩意儿。 不过并非所有的 Linux distributions 都默认会把他打开的，所以呢，某些时刻我们必须要手动将他激活才行。 激活的方法很简单，就是这样：  

```
[root@www ~]# /etc/init.d/atd restart
正在停止 atd:                          [  确定  ]
正在启动 atd:                          [  确定  ]
```

再配置一下启动时就启动这个服务，免得每次重新启动都得再来一次！  
 
```
[root@www ~]# chkconfig atd on
```

#### at
at 的工作情况其实是这样的：  
- 先找寻 /etc/at.allow 这个文件，写在这个文件中的使用者才能使用 at ，没有在这个文件中的使用者则不能使用 at (即使没有写在 at.deny 当中)；
- 如果 /etc/at.allow 不存在，就寻找 /etc/at.deny 这个文件，若写在这个 at.deny 的使用者则不能使用 at ，而没有在这个 at.deny 文件中的使用者，就可以使用 at 咯；
- 如果两个文件都不存在，那么只有 root 可以使用 at 这个命令。

```
# at -?
# at [-V] [-q x] [-f file] [-mMlbv] timespec ...
       at [-V] [-q x] [-f file] [-mMlbv] -t time
       at -c job ...
       atq [-V] [-q x]
       at [ -rd ] job ...
       atrm [-V] job ...
       
选项与参数：
-m  ：当 at 的工作完成后，即使没有输出信息，亦以 email 通知使用者该工作已完成。
-l  ：at -l 相当於 atq，列出目前系统上面的所有该使用者的 at 排程；
-d  ：at -d 相当於 atrm ，可以取消一个在 at 排程中的工作；
-v  ：可以使用较明显的时间格式列出 at 排程中的工作列表；
-c  ：可以列出后面接的该项工作的实际命令内容。
```

使用 at 时会进入一个 at shell 的环境来让使用者下达工作命令，且会跑到当时下达 at 命令的那个工作目录此时，建议你最好使用绝对路径来下达你的命令，比较不会有问题。  
at 的运行与终端机环境无关，而所有 stdout/stderr 都会传送到运行者的 mailbox 。  如果在 at shell 内的命令并没有任何的信息输出，那么 at 默认不会发 email 给运行者的，除非使用『 at -m 时间格式 』  

#### crontab 
 /etc/cron.allow  /etc/cron.deny , 工作会被纪录到/var/spool/cron/username 里

```
[root@www ~]# crontab [-u username] [-l|-e|-r]
选项与参数：
-u  ：只有 root 才能进行这个任务，亦即帮其他使用者创建/移除 crontab 工作排程；
-e  ：编辑 crontab 的工作内容, 进入 vi 的编辑画面
-l  ：查阅 crontab 的工作内容
-r  ：移除所有的 crontab 的工作内容，若仅要移除一项，请用 -e 去编辑。
```
编辑语法请参考：http://vbird.dic.ksu.edu.tw/linux_basic/0430cron_3.php#etc_crontab1  

`crontab -e`是针对使用者的 cron 来设计的，如果是『系统的例行性任务』时， 该怎么办呢？是否还是需要以`crontab -e`来管理你的例行性工作排程呢？当然不需要，你只要编辑 `/etc/crontab` 这个文件就可以啦！

基本上，`cron` 这个服务的最低侦测限制是『分钟』，所以『 `cron` 会每分钟去读取一次 `/etc/crontab` 与 `/var/spool/cron/username` 里面的数据内容 』，因此，只要你编辑完 `/etc/crontab` 这个文件，并且将他储存之后，那么 cron 的配置就自动的会来运行了

#### anacron 
anacron 处理非 24 小时一直启动的 Linux 系统的 crontab 的运行  

以天为单位或者是在启动后立刻进行 anacron 的动作，anacron 会去侦测停机期间应该进行但是并没有进行的 crontab 任务，并将该任务运行一遍后，anacron 就会自动停止了。
  
anacron 会以一天、七天、一个月为期去侦测系统未进行的 crontab 任务.  

那么 anacron 又是怎么知道我们的系统啥时关机的呢？  
这就得要使用 anacron 读取的时间记录档 (timestamps) 了！ anacron 会去分析现在的时间与时间记录档所记载的上次运行 anacron 的时间，两者比较后若发现有差异， 那就是在某些时刻没有进行 crontab 罗！此时 anacron 就会开始运行未进行的 crontab 任务了！ 所以 anacron 其实也是透过 crontab 来运行的！**因此 anacron 运行的时间通常有两个，一个是系统启动期间运行，一个是写入 crontab 的排程中**。 这样才能够在特定时间分析系统未进行的 crontab 工作嘛！

anacron 其实是一支程序并非一个服务！这支程序在 CentOS 当中已经进入 crontab 的排程喔！ 不相信吗？你可以这样追踪看看：

```
[root@www ~]# ll /etc/cron*/*ana*
-rwxr-xr-x 1 root root 379 Mar 28  2007 /etc/cron.daily/0anacron
-rwxr-xr-x 1 root root 381 Mar 28  2007 /etc/cron.monthly/0anacron
-rwxr-xr-x 1 root root 380 Mar 28  2007 /etc/cron.weekly/0anacron
# 刚好是每天、每周、每月有排程的工作目录！查阅一下每天的任务

[root@www ~]# cat /etc/cron.daily/0anacron
if [ ! -e /var/run/anacron.pid ]; then
    anacron -u cron.daily
fi
# 所以其实也仅是运行 anacron -u 的命令！
```
anacron 的语法如下：

```
[root@www ~]# anacron [-sfn] [job]..
[root@www ~]# anacron -u [job]..
选项与参数：
-s  ：开始一连续的运行各项工作 (job)，会依据时间记录档的数据判断是否进行；
-f  ：强制进行，而不去判断时间记录档的时间戳记；
-n  ：立刻进行未进行的任务，而不延迟 (delay) 等待时间；
-u  ：仅升级时间记录档的时间戳记，不进行任何工作。
job ：由 /etc/anacrontab 定义的各项工作名称。
```


## 第十七章、程序管理

#### 程序

fork and exec：程序呼叫的流程

程序都会藉由父程序以复制 (fork) 的方式产生一个一模一样的子程序， 然后被复制出来的子程序再以 exec 的方式来运行实际要进行的程序，最终就成为一个子程序的存在。 

(1)系统先以 fork 的方式复制一个与父程序相同的缓存程序，这个程序与父程序唯一的差别就是 PID 不同！ 但是这个缓存程序还会多一个 PPID 的参数，PPID 如前所述，就是父程序的程序识别码啦！然后(2)缓存程序开始以 exec 的方式加载实际要运行的程序，以上述图示来讲，新的程序名称为 qqq ，最终子程序的程序码就会变成 qqq 

服务(daemon)：常驻在内存的程序

#### 工作管理 (job control)
> 这个工作管理 (job control) 是用在 bash 环境下的，也就是说：『进行工作管理的行为中， 其实每个工作都是目前 bash 的子程序，亦即彼此之间是有相关性的。 我们无法以 job control 的方式由 tty1 的环境去管理 tty2 的 bash ; 当我们登陆系统取得 bash shell 之后，在单一终端机介面下同时进行多个工作的行为管理 』

- 我们可以在 /etc/security/limits.conf 里面配置使用者同时可以登陆的连线数
- 背景：可以自行运行的工作，你无法使用 [ctrl]+c 终止他，可使用 bg/fg 呼叫该工作
- 背景里面的工作状态又可以分为『暂停 (stop)』与『运行中 (running)』

**&** 直接将命令丢到背景中『运行』  
**[ctrl]-z** 将『目前』的工作丢到背景中『暂停』  
**jobs** 观察目前的背景工作状态  
**fg** 将背景工作拿到前景来处理: fg %jobnumber   
**bg** 让工作在背景下的状态变成运行中:  bg %jobnumber    
**kill** 管理背景当中的工作: kill -signal PID / %jobnumber  

工作管理当中提到的『背景』指的是在终端机模式下可以避免 [crtl]-c 中断的一个情境， 并不是放到系统的背景去喔！所以，工作管理的背景依旧与终端机有关, 若工作尚未结束的情况下你离线了，该工作会被中断掉！

如果我的工作需要进行一大段时间，我又不能放置在背景底下，那该如何处理呢？

**at**: at 是将工作放置到系统背景， 而与终端机无关。   
**nohup**: nohup 可以让你在离线或注销系统后，还能够让工作继续进行。
> 需要注意的是，nohup 并不支持 bash 内建的命令，因此你的命令必须要是外部命令才行

```
[root@www ~]# nohup [命令与参数]   <==在终端机前景中工作
[root@www ~]# nohup [命令与参数] & <==在终端机背景中工作
```

#### 程序管理
　1 程序的观察： ps (ps -l, ps aux, zombie), top, pstree  
　2 程序的管理： signal, kill, killall  
　3 关於程序的运行顺序： priority, nice, renice  
　4 系统资源的观察： free, uname, uptime, netstat, dmesg, vmstat  
　
>ps

```
ps -l :仅列出与你的操作环境 (bash) 有关的程序
ps aux :查阅所有系统运行的程序
ps axjf :列出类似程序树的程序显示
```

僵尸(zombie)程序是什么？  
> 通常，造成僵尸程序的成因是因为该程序应该已经运行完毕，或者是因故应该要终止了， 但是该程序的父程序却无法完整的将该程序结束掉，而造成那个程序一直存在内存当中。 如果你发现在某个程序的 CMD 后面还接上 defunct 时，就代表该程序是僵尸程序啦.  
> 事实上，通常僵尸程序都已经无法控管，而直接是交给 init 这支程序来负责了，偏偏 init 是系统第一支运行的程序， 他是所有程序的父程序！我们无法杀掉该程序的 (杀掉他，系统就死掉了！)，所以罗，如果产生僵尸程序， 而系统过一阵子还没有办法透过核心非经常性的特殊处理来将该程序删除时，那你只好透过 reboot 的方式来将该程序抹去了.

```
[root@www ~]# ps -l
F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
4 S     0 13639 13637  0  75   0 -  1287 wait   pts/1    00:00:00 bash
4 R     0 13700 13639  0  77   0 -  1101 -      pts/1    00:00:00 ps

F：Process Flags，说明这个程序的总结权限，常见号码有：
若为 4 表示此程序的权限为 root ；
若为 1 则表示此子程序仅进行复制(fork)而没有实际运行(exec)。

S：代表这个程序的状态 (STAT)，主要的状态有：
R (Running)：该程序正在运行中；
S (Sleep)：该程序目前正在睡眠状态(idle)，但可以被唤醒(signal)。
D ：不可被唤醒的睡眠状态，通常这支程序可能在等待 I/O 的情况(ex>列印)
T ：停止状态(stop)，可能是在工作控制(背景暂停)或除错 (traced) 状态；
Z (Zombie)：僵尸状态，程序已经终止但却无法被移除至内存外。
```

```
[root@www ~]# ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.0   2064   616 ?        Ss   Mar11   0:01 init [5]
root     18593  0.0  0.0   2240   476 ?        Ss   Mar14   0:00 /usr/sbin/atd

VSZ ：该 process 使用掉的虚拟内存量 (Kbytes)
RSS ：该 process 占用的固定的内存量 (Kbytes)
TTY ：该 process 是在那个终端机上面运行，若与终端机无关则显示 ?，另外， tty1-tty6 是本机上面的登陆者程序，若为 pts/0 等等的，则表示为由网络连接进主机的程序。
STAT：该程序目前的状态，状态显示与 ps -l 的 S 旗标相同 (R/S/T/Z)
START：该 process 被触发启动的时间
TIME ：该 process 实际使用 CPU 运行的时间
```

>top

```
# top -b -n 2 > /tmp/top.txt 将top进行2次,然后将结果输出到 /tmp/top.txt
# top -d 2 -p 13639
# top [-d 数字] | top [-bnp]
选项与参数：
-d  ：后面可以接秒数，就是整个程序画面升级的秒数。默认是 5 秒；
-b  ：以批量的方式运行 top ，还有更多的参数可以使用喔！
      通常会搭配数据流重导向来将批量的结果输出成为文件。
-n  ：与 -b 搭配，意义是，需要进行几次 top 的输出结果。
-p  ：指定某些个 PID 来进行观察监测而已。
在 top 运行过程当中可以使用的按键命令：
	? ：显示在 top 当中可以输入的按键命令；
	P ：以 CPU 的使用资源排序显示；
	M ：以 Memory 的使用资源排序显示；
	N ：以 PID 来排序喔！
	T ：由该 Process 使用的 CPU 时间累积 (TIME+) 排序。
	k ：给予某个 PID 一个讯号  (signal)
	r ：给予某个 PID 重新制订一个 nice 值。
	q ：离开 top 软件的按键。
```
>pstree

```
[root@www ~]# pstree [-A|U] [-up]
选项与参数：
-A  ：各程序树之间的连接以 ASCII 字节来连接；
-U  ：各程序树之间的连接以万国码的字节来连接。在某些终端介面下可能会有错误；
-p  ：并同时列出每个 process 的 PID；
-u  ：并同时列出每个 process 的所属帐号名称。
```
> kill -signal PID

| code | name | content |
|------|------|---------|
|1|SIGHUP|启动被终止的程序,可让该PID重新读取自己的配置档,类似重新启动|
|2|SIGINT|相当於用键盘输入 [ctrl]-c 来中断一个程序的进行|
|9|SIGKILL|代表强制中断一个程序的进行,可能会有"半产品"产生|
|15|SIGTERM|以正常的结束程序来终止该程序;但如果该程序已经发生问题,SIGTERM也无法正常终止|
|17|SIGSTOP| 相当於用键盘输入 [ctrl]-z 来暂停一个程序的进行|

> killall -signal  [-iIe]  命令名称 : 给予这个命令启动的所有PID讯号 

```
[root@www ~]# killall -9 httpd :强制终止所有以 httpd 启动的程序
[root@www ~]# killall -i -9 bash :依次询问每个 bash 程序是否需要被终止运行！
[root@www ~]# killall -1 syslogd :给予syslogd命令启动的PID一个SIGHUP的讯号

[root@www ~]# killall [-iIe] [command name]
选项与参数：
-i  ：interactive 的意思，互动式的，若需要删除时，会出现提示字节给使用者；
-e  ：exact 的意思，表示『后面接的 command name 要一致』，但整个完整的命令
      不能超过 15 个字节。
-I  ：命令名称(可能含参数)忽略大小写。
```
>PRI : priority

```   
Linux给予程序的优先运行序 (priority), PRI值越低代表越优先的意思。  
不过这个PRI值是由核心动态调整的,使用者无法直接调整 PRI 值的
```
> NI : nice

```
如果想要调整程序的优先运行序时，就得要透过 Nice 值了，Nice 值可以有正负的。
```

> PRI(new) ≈ PRI(old) + nice . 因为 PRI 是系统『动态』决定的，所以，虽然 nice 值是可以影响 PRI ，不过， 最终的 PRI 仍是要经过系统分析后才会决定的。

- 设置nice时，整个 nice 值是可以在父程序 --> 子程序之间传递
- nice 值可调整的范围为 -20 ~ 19 ；
- root 可随意调整自己或他人程序的 Nice 值，且范围为 -20 ~ 19 ；
- 一般使用者仅可调整自己程序的 Nice 值，且范围仅为 0 ~ 19 (避免一般用户抢占系统资源)；
- 一般使用者仅可将 nice 值越调越高，例如本来 nice 为 5 ，则未来仅能调整到大於 5；

> 调整该程序的 nice 值

- 一开始运行程序就立即给予一个特定的 nice 值：用 nice 命令 等
- 调整某个已经存在的 PID 的 nice 值：用 renice 命令, top 命令 等

> free ：观察内存使用情况  
> uname：查阅系统与核心相关资讯  
> uptime：观察系统启动时间与1,5,15分钟的平均负载
> netstat ：追踪网络或插槽档

```
# free
             total       used       free     shared    buffers     cached
Mem:       4047576    2655228    1392348        472     312412      51900
-/+ buffers/cache:    2290916    1756660
Swap:       499688     499688          0

shared/buffers/cached 则是在已被使用的量当中，用来作为共享，缓冲及缓存的量
系统是『很有效率的将所有的内存用光光』， 目的是为了让系统的存取效能加速！ 
swap 最好不要被使用超过 20% 以上， 因为swap的效能跟实体内存实在差很多，
而系统会使用到swap，绝对是因为实体内存不足了才会这样做的.
```
```
[root@www ~]# netstat -[atunlp]
选项与参数：
-a  ：将目前系统上所有的连线、监听、Socket 数据都列出来
-t  ：列出 tcp 网络封包的数据
-u  ：列出 udp 网络封包的数据
-n  ：不以程序的服务名称，以埠号 (port number) 来显示；
-l  ：列出目前正在网络监听 (listen) 的服务；
-p  ：列出该网络服务的程序 PID
```

> dmesg ：分析核心产生的信息

系统在启动的时候，核心会去侦测系统的硬件，你的某些硬件到底有没有被捉到，那就与这个时候的侦测有关, 使用 dmesg 就能把核心侦测的信息捉出来！

所有核心侦测的信息，不管是启动时候还是系统运行过程中，反正只要是核心产生的信息，都会被记录到内存中的某个保护区段。 dmesg 这个命令就能够将该区段的信息读出来的！因为信息实在太多了，所以运行时用 dmesg | more 来使画面暂停.

> vmstat ：侦测系统资源变化

vmstat 可以侦测『 CPU / 内存 / 磁碟输入输出状态 』等等，如果你想要了解一部繁忙的系统到底是哪个环节最累人， 可以使用 vmstat 分析看看

```
[root@www ~]# vmstat [-a] [延迟 [总计侦测次数]]   <==CPU/内存等资讯
[root@www ~]# vmstat [-fs]                     <==内存相关
[root@www ~]# vmstat [-S 单位]                  <==配置显示数据的单位
[root@www ~]# vmstat [-d]                      <==与磁碟有关
[root@www ~]# vmstat [-p 分割槽]                <==与磁碟有关
选项与参数：
-a  ：使用 inactive/active(活跃与否) 取代 buffer/cache 的内存输出资讯；
-f  ：启动到目前为止，系统复制 (fork) 的程序数；
-s  ：将一些事件 (启动至目前为止) 导致的内存变化情况列表说明；
-S  ：后面可以接单位，让显示的数据有单位。例如 K/M 取代 bytes 的容量；
-d  ：列出磁碟的读写总量统计表
-p  ：后面列出分割槽，可显示该分割槽的读写总量统计表

范例一：统计目前主机 CPU 状态，每秒一次，共计三次！
[root@www ~]# vmstat 1 3

procs -----------memory---------- ---swap-- -----io---- --system-- -----cpu------
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 0  0     28  61540 137000 291960    0    0     4     5   38   55  0  0 100  0  0
 0  0     28  61540 137000 291960    0    0     0     0 1004   50  0  0 100  0  0
 0  0     28  61540 137000 291964    0    0     0     0 1022   65  0  0 100  0  0
 
```

```
那么上面的表格各项栏位的意义为何？ 基本说明如下：

内存栏位 (procs) 的项目分别为：
r ：等待运行中的程序数量；b：不可被唤醒的程序数量。这两个项目越多，代表系统越忙碌 
(因为系统太忙，所以很多程序就无法被运行或一直在等待而无法被唤醒之故)。

内存栏位 (memory) 项目分别为：
swpd：虚拟内存被使用的容量； free：未被使用的内存容量； buff：用於缓冲内存； 
cache：用於高速缓存。 这部份则与 free 是相同的。

内存置换空间 (swap) 的项目分别为：
si：由磁碟中将程序取出的量； so：由於内存不足而将没用到的程序写入到磁碟的 swap 的容量。
如果 si/so 的数值太大，表示内存内的数据常常得在磁碟与主内存之间传来传去，系统效能会很差！

磁碟读写 (io) 的项目分别为：
bi：由磁碟写入的区块数量； bo：写入到磁碟去的区块数量。
如果这部份的值越高，代表系统的 I/O 非常忙碌！

系统 (system) 的项目分别为：
in：每秒被中断的程序次数； cs：每秒钟进行的事件切换次数；
这两个数值越大，代表系统与周边设备的沟通非常频繁！ 这些周边设备当然包括磁碟、网络卡、时间钟等。

CPU 的项目分别为：
us：非核心层的 CPU 使用状态； sy：核心层所使用的 CPU 状态； 
id：闲置的状态； wa：等待 I/O 所耗费的 CPU 状态；
 st：被虚拟机器 (virtual machine) 所盗用的 CPU 使用状态 (2.6.11 以后才支持)。
```

> fuser：藉由文件(或文件系统)找出正在使用该文件的程序

```
fuser: [-cfu] file ...
	-c	file is treated as mount point
	-f	the report is only for the named files
	-u	print username of pid in parenthesis
```
> lsof ：列出被程序所开启或使用的文件与装置

```
[root@www ~]# lsof [-aUu] [+d]
选项与参数：
-a  ：多项数据需要『同时成立』才显示出结果时！
-U  ：仅列出 Unix like 系统的 socket 文件类型；
-u  ：后面接 username，列出该使用者相关程序所开启的文件；
+d  ：后面接目录，亦即找出某个目录底下已经被开启的文件！

[root@www ~]# lsof :列出目前系统上面所有已经被开启的文件与装置
[root@www ~]# lsof -u root -a -U :仅列出关於root的所有程序开启的socket文件
``` 
>pidof ：找出某支正在运行的程序的 PID

```
[root@www ~]# pidof [-sx] program_name
选项与参数：
-s  ：仅列出一个 PID 而不列出所有的 PID
-x  ：同时列出该 program name 可能的 PPID 那个程序的 PID

[root@www ~]# pidof init syslogd : 列出目前系统上面这两个程序的 PID
1 4286
```

#### [SELinux](http://vbird.dic.ksu.edu.tw/linux_basic/0440processcontrol.php#selinux) : Security Enhanced Linux

> 其实 SELinux 是在进行程序、文件等细部权限配置依据的一个核心模块！ 由於启动网络服务的也是程序，因此刚好也能够控制网络服务能否存取系统资源的一道关卡！

> DAC : 自主式存取控制 (Discretionary Access Control) 就是依据程序的拥有者与文件资源的 rwx 权限来决定有无存取的能力  
> MAC : 委任式存取控制 (Mandatory Access Control) 可以针对特定的程序与特定的文件资源来进行权限的控管

SELinux 是透过 MAC 的方式来控管程序，他控制的主体是程序， 而目标则是该程序能否读取的『文件资源』
