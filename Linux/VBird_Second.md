## 鸟哥的Linux私房菜基础篇_中 -- Daemon、登录档、启动流程与模块管理  
<!-- toc -->
####<a name=18>[第十八章、认识系统服务(daemons)](http://vbird.dic.ksu.edu.tw/linux_basic/0560daemons.php)</a>

**service 与 daemon 区分**：  
service是系统为了某些功能提供的一些服务，daemon就是实现service在背景中运行的程序, daemon命名后面常带d。平时可以将这两者视为相同,因为达成某个service是需要一支daemon在背景中运行， 没有这支daemon就不会有service。

**daemon 的主要分类**：   

- stand alone：此 daemon 可以自行单独启动服务  
- super daemon：一支特殊的 daemon 来统一管理一些服务

**daemon 提供服务的的工作状态类型**：

- signal-control:这种 daemon 是透过讯号来管理的，只要有任何客户端的需求进来，他就会立即启动去处理！如：打印机的服务 (cupsd)    
- interval-control: 这种 daemon 则主要是『每隔一段时间就主动的去运行某项工作』，所以，你要作的是在配置文件指定服务要进行的时间与工作， 该服务在指定的时间才会去完成工作。如：atd 与 crond 

`cat /etc/services` 查看 daemon 与 port number 以及网络协议的对应关系

**daemon 的某些重要的配置文件**：

- /etc/init.d/* ：启动脚本放置处
- /etc/sysconfig/* ：各服务的初始化环境配置文件
- /etc/xinetd.conf ： super daemon配置文件
- /etc/xinetd.d/* ：super daemon所管理的其他 daemon 的配
- /etc/* ：各服务各自的配置文件
- /var/lib/* ：各服务产生的数据库，如MySQL默认写入/var/lib/mysql/
- /var/run/* ：各服务的程序之 PID 记录处

基本上只要一个服务受到 xinetd(super daemon)管理，或者是该服务的程序支持 TCP Wrappers 函式的功能时，都可以透过 **/etc/hosts.allow, /etc/hosts.deny** 来配置防火墙。换个方式来说，只要不支持 TCP Wrappers 函式功能的软件程序就无法使用 /etc/hosts.{allow,deny} 的配置值。

```
# 测试sshd程序有无支持 TCP Wrappers 的功能
# ldd(library dependency discovery)查询程序的动态函式库支持
[root@www ~]# ldd $(which sshd) 
        linux-vdso.so.1 =>  (0x00007fffda0eb000)
        libwrap.so.0 => /usr/lib64/libwrap.so.0 (0x00002abcbfaed000)
        libpam.so.0 => /lib64/libpam.so.0 (0x00002abcbfcf6000)
        ........
```
- **/etc/hosts.{allow,deny}** 配置文件语法: 
`<service name> : <IP, domain or hostname> : <action>`
- **service name** 其实是启动该服务的程序, 如sshd程序启动ssh服务
- 写在 **hosts.allow** 当中的 IP 与网段默认为可通行，即第三栏的 allow 可省略
- 写在 **hosts.deny** 当中的 IP 与网段则默认为 deny，即第三栏的 deny 可省略
- 这两个文件的判断依据是：(1)以 **hosts.allow** 为优先，而(2)若分析到的 IP 或网段并没有记录在hosts.allow，则以**hosts.deny**来判断。

还可以使用一些特殊参数在第一及第二个字段，内容有：

```
ALL：代表全部的 program name 或者是 IP 都接受的意思，例如 ALL: ALL: deny
LOCAL：代表来自本机的意思，例如： ALL: LOCAL: allow
UNKNOWN：代表不知道的 IP 或者是 domain 或者是服务时；
KNOWN：代表为可解析的 IP, domain 等等信息时；
```

找出目前系统开启的网络服务有哪些  [root@www ~]# netstat -tulp  
观察所有的服务状态  [root@www ~]# service --status-all

**Linux 主机是怎么启动的呢 ？**

```
1 打开计算机电源，开始读取 BIOS 并进行主机的自我测试；
2 透过 BIOS 取得第一个可启动装置，读取主要启动区 (MBR) 取得启动管理程序；
3 透过启动管理程序的配置，取得 kernel 并加载内存且侦测系统硬件；
4 核心主动呼叫 init 程序；
5 init 程序开始运行系统初始化 (/etc/rc.d/rc.sysinit)
6 依据 init 的配置进行 daemon start (/etc/rc.d/rc[0-6].d/*), 
  在不同的运行等级呼叫不同的服务
7 加载本机配置 (/etc/rc.d/rc.local)
```

在启动 Linux 系统时，可以进入不同的模式喔，这模式我们称为运行等级 (run level)。不同的运行等级有不同的功能与服务，窗口接口的run level是5，纯文本界面是3。

```
[root@www ~]# /etc/init.d/rsync status
 * rsync is not running

# chkconfig： 配置自己的系统服务
[root@www ~]# chkconfig [--add|--del] [服务名称]
选项与参数：
--add ：添加一个服务名称给 chkconfig 来管理，该服务名称必须在 /etc/init.d/ 内
--del ：删除一个给 chkconfig 管理的服务

[root@www ~]# chkconfig --list [服务名称]
[root@www ~]# chkconfig [--level [0123456]] [服务名称] [on|off]
选项与参数：
--list ：仅将目前的各项服务状态栏出来
--level：配置某个服务在该 level 下启动 (on) 或关闭 (off)
``` 

Linux 默认启动的常用服务

|Service Name | Info|
|-------------|-----|
| atd |(系统)单一的例行性工作排程, 抵挡机制的配置文件在 /etc/at.{allow,deny} |
| anacron|(系统)与循环型的工作排程 cron 有关，可在排程过期后还可以唤醒来继续运行，配置文件在 /etc/anacrontab，可关闭|
| crond |(系统)系统配置文件为 /etc/crontab|
|iptables|(网络)本机防火墙功能, 是核心支持, 不能够取消|
|network|(网络)提供网络配置的功能，所以一定要启动的啦|
|sshd|(网络)这个是远程联机服务器的软件功能， 这个通讯协议比 telnet 好的地方在于 sshd 在传送数据时可以进行加密喔！这个服务不要关闭他！|
|syslog|(系统)这个服务可以记录系统所产生的各项信息， 包括 /var/log/messages 内的几个重要的登录档啊。|
|xinetd|(系统)就是 super daemon 啊|

Linux 其它非默认启动的常用服务

|Service Name | Info|
|-------------|-----|
|httpd	|(网络)这个服务可以让你的 Linux 服务器成为 www server|
|nfs |	(网络)这就是 Network Filesystem，是 Unix-Like 之间互相作为网络驱动器机的一个功能|
|ntpd|(网络)服务的全名是 Network Time Protocol ，这个服务可以用来进行网络校时， 让你系统的时间永远都是正确的哩！|
|vsftpd|(网络)作为文件传输服务器 (FTP) 的服务|

####<a name=19> [第十九章、认识与分析登录文件](http://vbird.dic.ksu.edu.tw/linux_basic/0570syslog.php)</a>

登录文件的重要性：
  
- 解决系统方面的错误  
- 解决网络服务的问题  
- 过往事件记录簿  

Linux 常见的登录文件档名：

| Name | Info |
| ---- | ---- |
|/var/log/cron|你的 crontab 排程有没有实际被进行？ 进行过程有没有发生错误？你的 /etc/crontab 是否撰写正确？在这个登录文件内查询看看|
|/var/log/dmesg|记录系统在启动的时候核心侦测过程所产生的各项资讯。由於 CentOS 默认将启动时核心的硬件侦测过程取消显示， 因此额外将数据记录一份在这个文件中|
|/var/log/lastlog|可以记录系统上面所有的帐号最近一次登陆系统时的相关资讯。第十四章的 lastlog 命令就是利用这个文件的记录资讯来显示的|
|/var/log/messages|这个文件相当的重要，几乎系统发生的错误信息 (或者是重要的资讯) 都会记录在这个文件中； 如果系统发生莫名的错误时，这个文件是一定要查阅的登录文件之一|
|/var/log/secure|基本上，只要牵涉到『需要输入帐号口令』的软件，那么当登陆时 (不管登陆正确或错误) 都会被记录在此文件中。 包括系统的 login 程序、图形介面登陆所使用的 gdm 程序、 su, sudo 等程序、还有网络连线的 ssh, telnet 等程序， 登陆资讯都会被记载在这里|
|/var/log/maillog, /var/log/mail/* | 记录邮件的往来资讯，其实主要是记录 sendmail (SMTP 协议提供者) 与 dovecot (POP3 协议提供者) 所产生的信息啦。 SMTP 是发信所使用的通讯协议， POP3 则是收信使用的通讯协议。 sendmail 与 dovecot 则分别是两套达成通讯协议的软件|
|/var/log/wtmp, /var/log/faillog| 这两个文件可以记录正确登陆系统者的帐号资讯 (wtmp) 与错误登陆时所使用的帐号资讯 (faillog) , 第十一章的 last 命令就是读取 wtmp 来显示的， 这对於追踪一般帐号者的使用行为很有帮助|
|/var/log/httpd/\*, /var/log/news/\*, /var/log/samba/*| 不同的网络服务会使用它们自己的登录文件来记载它们自己产生的各项信息！上述的目录内则是个别服务所制订的登录文件|

针对登录文件所需的功能，我们需要的服务与程序有： 

- **syslogd**：主要登录系统与网络等服务的信息；
- **klogd**：主要登录核心产生的各项资讯；
- **logrotate**：主要在进行登录文件的轮替功能。

syslog 利用的是 daemon 的方式来启动的， 当有需求的时候立刻就会被运行的，但是 logrotate 却是在规定的时间到了之后才来进行登录文件的轮替， 所以这个 logrotate 程序当然就是挂在 cron 底下进行的, /etc/cron.daily/logrotate 就是记录了每天要进行的登录文件轮替的行.

一般来说，系统产生的信息经过 syslog 而记录下来的数据中，每条信息均会记录底下的几个重要数据：

- 事件发生的日期与时间；
- 发生此事件的主机名称；
- 启动此事件的服务名称 (如 samba, xinetd 等) 或函式名称 (如 libpam ..)；
- 该信息的实际数据内容  

基本上，syslog 针对各种服务与信息记录在某些文件的配置档就是 /etc/syslog.conf， 这个文件规定了：(1)什么服务 (2)的什么等级信息 (3)需要被记录在哪里(装置或文件) 

```
# chattr a: 只能被添加，而不能被删除
[root@www ~]# chattr +a /var/log/messages
[root@www ~]# lsattr /var/log/messages
-----a------- /var/log/messages
[root@www ~]# chattr -a /var/log/messages
```

N台Clients将登陆文件传送到服务器去：

```
# Server 端：修改 syslogd 的启动配置档，通常在 /etc/sysconfig 内！
[root@www ~]# vim /etc/sysconfig/syslog
# 找到底下这一行：
SYSLOGD_OPTIONS="-m 0"
# 改成底下这样子！
SYSLOGD_OPTIONS="-m 0 -r"

# 重新启动与观察 syslogd 喔！
[root@www ~]# /etc/init.d/syslog restart
[root@www ~]# netstat -lunp | grep syslog
Proto Recv-Q Send-Q Local Address  Foreign Address State   PID/Program name
udp        0      0 0.0.0.0:514    0.0.0.0:*               13981/syslogd

# Client 端：登陆文件传送到登录文件服务器（192.168.1.100）
[root@www ~]# vim /etc/syslog.conf
*.*       @192.168.1.100
```
**logrotate**

>logrotate 主要是针对登录文件来进行轮替的动作，他必须要记载『 在什么状态下才将登录文件进行轮替』的配置, 参数配置档是：/etc/logrotate.conf, /etc/logrotate.d/*

> logrotate.conf 才是主要的参数文件，至於 logrotate.d目录里面的所有文件都会被主动的读入 .conf 文件当中来进行！目录里面的文件中，如没有规定到细部配置，则以 .conf 文件的规定为默认值！

logrotate.conf 的配置语法是： 
 
```
登录文件的绝对路径档名 ... {  
		个别的参数配置值，如 monthly, compress 等等  
	} 
```
默认的/etc/logrotate.conf

```
[root@www ~]# vim /etc/logrotate.conf
# 底下的配置是 "logrotate 的默认配置值" ，如果个别的文件配置了其他的参数，
# 则将以个别的文件配置为主，若该文件没有配置到的参数则以这个文件的内容为默认值！

weekly    <==默认每个礼拜对登录文件进行一次 rotate 的工作
rotate 4  <==保留几个登录文件呢？默认是保留四个！
create    <==由於登录文件被更名，因此创建一个新的来继续储存之意！
#compress <==被更动的登录文件是否需要压缩？如果登录文件太大则可考虑此参数启动

include /etc/logrotate.d
# 将 /etc/logrotate.d/ 这个目录中的所有文件都读进来运行 rotate 的工作！

/var/log/wtmp {       <==仅针对 /var/log/wtmp 所配置的参数
    monthly           <==每个月一次，取代每周！
    minsize 1M        <==文件容量一定要超过 1M 后才进行 rotate (略过时间参数)
    create 0664 root utmp <==指定新建文件的权限与所属帐号/群组
    rotate 1          <==仅保留一个，亦即仅有 wtmp.1 保留而已。
}
```
假设你已经创建了 /var/log/admin.log 这个文件， 现在，你想要将该文件加上 +a 这个隐藏标签，而且配置底下的相关资讯：

- 登录文件轮替一个月进行一次；
- 该登录文件若大於 10MB 时，则主动进行轮替，不需要考虑一个月的期限；
- 保存五个备份文件；
- 备份文件需要压缩

那你可以怎么样配置呢

```
# 1. 先创建 +a 这个属性啊！
[root@www ~]# chattr +a /var/log/admin.log
[root@www ~]# mv /var/log/admin.log /var/log/admin.log.1
Operation not permitted # 确定加入了a的隐藏属性，root无法移动此登录文件！

# 2. 开始创建 logrotate 的配置档，添加一个文件在 /etc/logrotate.d 内就对了！
[root@www ~]# vi /etc/logrotate.d/admin
# This configuration is from VBird 2009/04/08
/var/log/admin.log {
        monthly   <==每个月进行一次
        size=10M  <==文件容量大於 10M 则开始处置
        rotate 5  <==保留五个！
        compress  <==进行压缩工作！
        sharedscripts
        prerotate
                /usr/bin/chattr -a /var/log/admin.log
        endscript
        sharedscripts
        postrotate
                /usr/bin/killall -HUP syslogd
                /usr/bin/chattr +a /var/log/admin.log
        endscript
}

# 3. 测试一下 logrotate 相关功能的资讯显示：
[root@www ~]# logrotate [-vf] logfile
选项与参数：
-v  ：启动显示模式，会显示 logrotate 运行的过程喔！
-f  ：不论是否符合配置档的数据，强制每个登录文件都进行 rotate 的动作！

[root@www ~]# logrotate -v /etc/logrotate.conf
reading config file /etc/logrotate.conf <==读取主要配置档
including /etc/logrotate.d              <==呼叫外部的配置
reading config file acpid               <==就是外部配置啊！
....(中间省略)....
Handling 21 logs                        <==共有 21 个登录文件被记录
....(中间省略)....
rotating pattern: /var/log/admin.log  10485760 bytes (5 rotations)
empty log files are rotated, old logs are removed
considering log /var/log/admin.log
  log does not need rotating
not running prerotate script, since no logs will be rotated
not running postrotate script, since no logs were rotated
....(底下省略)....
# 因为还不足一个月，文件也没有大於 10M，所以不需进行轮替！

# 4. 测试一下强制 logrotate 与相关功能的资讯显示：
[root@www ~]# logrotate -vf /etc/logrotate.d/admin
....(中间省略)....
rotating pattern: /var/log/admin.log  forced from command line (5 rotations)
empty log files are rotated, old logs are removed
considering log /var/log/admin.log
  log needs rotating
rotating log /var/log/admin.log, log->rotateCount is 5
renaming /var/log/admin.log.5.gz to /var/log/admin.log.6.gz (rotatecount 5, logstart 1, i 5),
old log /var/log/admin.log.5.gz does not exist
....(中间省略)....
renaming /var/log/admin.log.0.gz to /var/log/admin.log.1.gz (rotatecount 5, logstart 1, i 0),
old log /var/log/admin.log.0.gz does not exist
log /var/log/admin.log.6.gz doesn't exist -- won't try to dispose of it
running prerotate script
renaming /var/log/admin.log to /var/log/admin.log.1
running postrotate script
compressing log with: /bin/gzip

# 5. 检查登陆文件是否正确被轮替
[root@www ~]# lsattr /var/log/admin.log*
-----a------- /var/log/admin.log       <==新文件仍然含有a属性！
------------- /var/log/admin.log.1.gz  <==有压缩过喔！
```

####<a name=20> [第二十章、启动流程、模块管理与 Loader](http://vbird.dic.ksu.edu.tw/linux_basic/0510osloader.php)</a>

##### 1. [Linux 的启动流程分析](http://vbird.dic.ksu.edu.tw/linux_basic/0510osloader.php#startup)

系统启动的经过可以汇整成底下的流程的：

1. 加载 BIOS 的硬件资讯与进行自我测试，并依据配置取得第一个可启动的装置；
2. 读取并运行第一个启动装置内 MBR 的 boot Loader (亦即是 grub, spfdisk 等程序)；
3. 依据 boot loader 的配置加载 Kernel ，Kernel 会开始侦测硬件与加载驱动程序；
4. 在硬件驱动成功后，Kernel 会主动呼叫 init 程序，而 init 会取得 run-level 资讯；
5. init 运行 /etc/rc.d/rc.sysinit 文件来准备软件运行的作业环境 (如网络、时区等)；
6. init 运行 run-level 的各个服务之启动 (script 方式)；
7. init 运行 /etc/rc.d/rc.local 文件；
8. init 运行终端机模拟程序 mingetty 来启动 login 程序，最后就等待使用者登陆；

由於我们的系统软件大多放置到硬盘中, 所以 BIOS 会指定启动的装置好让我们可以读取磁碟中的操作系统核心文件。 但由於不同的操作系统他的文件系统格式不相同，因此我们必须要以一个启动管理程序来处理核心文件加载 (load) 的问题， 因此这个启动管理程序就被称为 Boot Loader 了。那这个 Boot Loader 程序安装在哪里呢？就在启动装置的第一个磁区 (sector) 内，也就是我们一直谈到的 MBR (Master Boot Record, 主要启动记录区)。

**Boot Loader 的功能**

1. 提供菜单：使用者可以选择不同的启动项目，这也是多重启动的重要功能！
2. 加载核心文件：直接指向可启动的程序区段来开始操作系统；
3. 转交其他 loader：将启动管理功能转交给其他 loader 负责。

**加载核心侦测硬件与 initrd 的功能**

当我们藉由 boot loader 的管理而开始读取核心文件后，接下来， Linux 就会将核心解压缩到主内存当中， 并且利用核心的功能，开始测试与驱动各个周边装置，包括储存装置、CPU、网络卡、声卡等等。 此时 Linux 核心会以自己的功能重新侦测一次硬件，而不一定会使用 BIOS 侦测到的硬件资讯喔！

那么核心文件一般会被放置到 `/boot` 里面，并且取名为 `/boot/vmlinuz` 

```
[root@www ~]# ls --format=single-column -F /boot
config-2.6.18-92.el5      <==此版本核心被编译时选择的功能与模块配置档
grub/                     <==就是启动管理程序 grub 相关数据目录
initrd-2.6.18-92.el5.img  <==虚拟文件系统档！
System.map-2.6.18-92.el5  <==核心功能放置到内存位址的对应表
vmlinuz-2.6.18-92.el5     <==就是核心文件啦！最重要者！
```

虚拟文件系统 (Initial RAM Disk) 一般使用的档名为 `/boot/initrd` ，这个文件的特色是，他也能够透过 boot loader 来加载到内存中， 然后这个文件会被解压缩并且在内存当中模拟成一个根目录， 且此模拟在内存当中的文件系统能够提供一支可运行的程序，透过该程序来加载启动过程中所最需要的核心模块， 通常这些模块就是 U盘, RAID, LVM, SCSI 等文件系统与磁碟介面的驱动程序啦！等加载完成后， 会帮助核心重新呼叫 `/sbin/init` 来开始后续的正常启动流程。

![](/images/vbird01.jpg)

如上图所示，boot loader 可以加载 kernel 与 initrd ，然后在内存中让 initrd 解压缩成为根目录， kernel 就能够藉此加载适当的驱动程序，最终释放虚拟文件系统，并挂载实际的根目录文件系统， 就能够开始后续的正常启动流程。

**第一支程序 /sbin/init 及配置档 /etc/inittab 与 runlevel**

/sbin/init 最主要的功能就是准备软件运行的环境，包括系统的主机名称、网络配置、语系处理、文件系统格式及其他服务的启动等。 而所有的动作都会透过 init 的配置档，亦即是 /etc/inittab 来规划，而 inittab 内还有一个很重要的配置项目，那就是默认的 runlevel.

基本上，依据有无网络与有无 X Window 而将 run level 分为 7 个等级，分别是：

```
0 - halt (系统直接关机)
1 - single user mode (单人维护模式，用在系统出问题时的维护)
2 - Multi-user, without NFS (类似底下的 runlevel 3，但无 NFS 服务)
3 - Full multi-user mode (完整含有网络功能的纯文字模式)
4 - unused (系统保留功能)
5 - X11 (与 runlevel 3 类似，但加载使用 X Window)
6 - reboot (重新启动)
```
/etc/inittab 的内容与语法

```
# 每个栏位的意义与说明
# [配置项目]:[run level]:[init 的动作行为]:[命令项目]

[root@www ~]# vim /etc/inittab
id:5:initdefault:    <==默认的 runlevel 配置, 此 runlevel 为 5 
si::sysinit:/etc/rc.d/rc.sysinit  <==准备系统软件运行的环境的脚本运行档

# 7 个不同 run level 的，需要启动的服务的 scripts 放置路径：
l0:0:wait:/etc/rc.d/rc 0    <==runlevel 0 在 /etc/rc.d/rc0.d/
l1:1:wait:/etc/rc.d/rc 1    <==runlevel 1 在 /etc/rc.d/rc1.d/
l2:2:wait:/etc/rc.d/rc 2    <==runlevel 2 在 /etc/rc.d/rc2.d/
l3:3:wait:/etc/rc.d/rc 3    <==runlevel 3 在 /etc/rc.d/rc3.d/
l4:4:wait:/etc/rc.d/rc 4    <==runlevel 4 在 /etc/rc.d/rc4.d/
l5:5:wait:/etc/rc.d/rc 5    <==runlevel 5 在 /etc/rc.d/rc5.d/
l6:6:wait:/etc/rc.d/rc 6    <==runlevel 6 在 /etc/rc.d/rc6.d/

# 是否允许按下 [ctrl]+[alt]+[del] 就重新启动的配置项目：
ca::ctrlaltdel:/sbin/shutdown -t3 -r now

# 底下两个配置则是关於不断电系统的 (UPS)，一个是没电力时的关机，一个是复电的处理
pf::powerfail:/sbin/shutdown -f -h +2 "Power Failure; System Shutting Down"
pr:12345:powerokwait:/sbin/shutdown -c "Power Restored; Shutdown Cancelled"

1:2345:respawn:/sbin/mingetty tty1  <==其实 tty1~tty6 是由底下这六行决定的。
2:2345:respawn:/sbin/mingetty tty2
3:2345:respawn:/sbin/mingetty tty3
4:2345:respawn:/sbin/mingetty tty4
5:2345:respawn:/sbin/mingetty tty5
6:2345:respawn:/sbin/mingetty tty6

x:5:respawn:/etc/X11/prefdm -nodaemon <==X window 则是这行决定的！
```

因此我们可以知道 CentOS 的 init 依据 inittab 配置的处理流程会是：

```
先取得 runlevel 亦即默认运行等级的相关等级 (以鸟哥的测试机为例，为 5 号)；
使用 /etc/rc.d/rc.sysinit 进行系统初始化
由於 runlevel 是 5 ，因此只进行『l5:5:wait:/etc/rc.d/rc 5』，其他行则略过
配置好 [ctrl]+[alt]+[del] 这组的组合键功能
配置不断电系统的 pf, pr 两种机制；
启动 mingetty 的六个终端机 (tty1 ~ tty6)
最终以 /etc/X11/perfdm -nodaemon 启动图形介面啦！
```

**[init 处理系统初始化流程 (/etc/rc.d/rc.sysinit)](http://vbird.dic.ksu.edu.tw/linux_basic/0510osloader.php#startup_sysinit)**

启动系统服务与相关启动配置档 (/etc/rc.d/rc N & /etc/sysconfig)

服务的启动主要是以『/etc/init.d/服务档名 {start,stop}』来启动与关闭的，那么透过刚刚 /etc/rc.d/rc 程序的解说，我们可以清楚的了解到了 /etc/rc5.d/[SK]xx 其实就是跑到 /etc/init.d/ 去找到相对应的服务脚本， 然后分别进行 start (Sxx) 或 stop (Kxx) 的动作而已啦！举例来说，以上述的表格内的 K91capi 及 S10network 为例好了， 透过 /etc/rc.d/rc 5 的运行，这两个文件会这样进行：

>/etc/rc5.d/K91capi stop --> /etc/init.d/capi stop  
/etc/rc5.d/S10network start --> /etc/init.d/network start

**启动过程会用到的主要配置档**:

/etc/modprobe.conf : 

> /etc/rc.d/rc.sysinit 加载的使用者自订模块就是在 /etc/sysconfig/modules/ 目录下，某些条件下我们还是得对模块进行一些参数的规划， 此时就得要使用到 /etc/modprobe.conf

/etc/sysconfig/* : 整个启动的过程当中，老是读取的一些服务的相关配置档都是记录在 /etc/sysconfig 目录下

**Run level 的切换**: 

> 要每次启动都运行某个默认的 run level ，则需要修改 /etc/inittab 内的配置项目， 亦即是『 id:5:initdefault: 』里头的数字啊；  
> 如果仅只是暂时变更系统的 run level 时，则使用 init [0-6] 来进行 run level 的变更。 但下次重新启动时，依旧会是以 /etc/inittab 的配置为准。

```
[root@www ~]# runlevel
N 5
# 左边代表前一个 runlevel ，右边代表目前的 runlevel。
# 由於之前并没有切换过 runlevel ，因此前一个 runlevel 不存在 (N)

# 将目前的 runlevel 切换成为 3 (注意，tty7(X Window)的数据会消失！)
[root@www ~]# init 3
NIT: Sending processes the TERM signal
Applying Intel CPU microcode update:        [  OK  ]
Starting background readahead:              [  OK  ]
Starting irqbalance:                        [  OK  ]
Starting httpd:                             [  OK  ]
Starting anacron:                           [  OK  ]
# 这代表，新的 runlevel 亦即是 runlevel3 比前一个 runlevel 多出了上述 5 个服务

[root@www ~]# runlevel
5 3 

[root@www ~]# init 0 # 关机 
[root@www ~]# init 6 # 重新启动
```

##### 2. [核心与核心模块](http://vbird.dic.ksu.edu.tw/linux_basic/0510osloader.php#kernel)

核心一般都是压缩档，因此在使用核心之前，就得要将他解压缩后， 才能加载主内存当中, 目前的核心都是具有模块化功能.

**核心与核心模块放在哪**？

>核心： /boot/vmlinuz 或 /boot/vmlinuz-version；  
核心解压缩所需 RAM Disk： /boot/initrd (/boot/initrd-version)；  
核心模块： /lib/modules/version/kernel 或 /lib/modules/$(uname -r)/kernel；  
核心原始码： /usr/src/linux 或 /usr/src/kernels/ (要安装才会有，默认不安装)

如果该核心被顺利的加载系统当中了，那么就会有几个资讯纪录下来：

>核心版本： /proc/version  
系统核心功能： /proc/sys/kernel

**核心模块的相依关系**：

```
# 检查 /lib/modules/$(uname -r)/modules.dep 这个文件啦！
# 他记录了在核心支持的模块的各项相依性
[root@www ~]# depmod [-Ane]
选项与参数：
-A  ：不加任何参数时， depmod 会主动的去分析目前核心的模块，并且重新写入
      /lib/modules/$(uname -r)/modules.dep 当中。若加入 -A 参数时，则 depmod
      会去搜寻比 modules.dep 内还要新的模块，如果真找到新模块，才会升级。
-n  ：不写入 modules.dep ，而是将结果输出到萤幕上(standard out)；
-e  ：显示出目前已加载的不可运行的模块名称
```

**核心模块的观察**：

```
[root@www ~]# lsmod
Module                  Size  Used by
8139too                28737  0
8139cp                 26305  0
mii                     9409  2 8139too,8139cp
....(省略)....

[root@www ~]# modinfo [-adln] [module_name|filename]
选项与参数：
-a  ：仅列出作者名称；
-d  ：仅列出该 modules 的说明 (description)；
-l  ：仅列出授权 (license)；
-n  ：仅列出该模块的详细路径。
```

**核心模块的加载与移除**:

modprobe 命令加载模块，会主动的去搜寻 modules.dep 的内容，先克服了模块的相依性后， 才决定需要加载的模块有哪些，很方便。  
insmod, rmmod 则完全由使用者自行加载或移除一个完整档名的模块， 并不会主动的分析模块相依性。

```
root@www ~]# modprobe [-lcfr] module_name
选项与参数：
-c  ：列出目前系统所有的模块！(更详细的代号对应表)
-l  ：列出目前在 /lib/modules/`uname -r`/kernel 当中的所有模块完整档名；
-f  ：强制加载该模块；
-r  ：类似 rmmod ，就是移除某个模块罗～

[root@www ~]# modprobe cifs  # 范例：加载 cifs 模块

[root@www ~]# insmod [/full/path/module_name] [parameters
[root@www ~]# insmod /lib/modules/$(uname -r)/kernel/fs/cifs/cifs.ko
[root@www ~]# rmmod cifs
```

**核心模块的额外参数配置**： /etc/modprobe.conf  
如果您想要修改某些模块的额外参数配置， 就在这个文件内配置

```
[root@www ~]# vi /etc/modprobe.conf
alias eth0 ne
alias eth1 ne
options eth0 io=0x300 irq=5
options eth1 io=0x320 irq=7
```

##### 3. [Boot loader: Grub](http://vbird.dic.ksu.edu.tw/linux_basic/0510osloader.php#grub)

Linux 将 boot loader 的程序码运行与配置值加载分成两个阶段 (stage) 来运行：

Stage 1：运行 boot loader 主程序：  

>第一阶段为运行 boot loader 的主程序，这个主程序必须要被安装在启动区，亦即是 MBR 或者是 boot sector 。因为 MBR 实在太小了( MBR 是整个硬盘的第一个 sector 内的一个区块，充其量整个大小也才 446 bytes )，所以，MBR 或 boot sector 通常仅安装 boot loader 的最小主程序， 并没有安装 loader 的相关配置档；

Stage 2：主程序加载配置档： 
 
>第二阶段为透过 boot loader 加载所有配置档与相关的环境参数文件 (包括文件系统定义与主要配置档 /boot/menu.lst 或 /boot/grub.conf ).

**启动时直接指定核心文件启动:**

```
[root@www ~]# vim /boot/grub/menu.lst
default=0     <==默认启动选项，使用第 1 个启动菜单 (title)
timeout=5     <==若 5 秒内未动键盘，使用默认菜单启动
splashimage=(hd0,0)/grub/splash.xpm.gz <==背景图示所在的文件
hiddenmenu    <==读秒期间是否显示出完整的菜单画面(默认隐藏)

title CentOS (2.6.18-92.el5)    <==第一个菜单的内容
# 1. 先指定核心文件放置的 partition，再读取文件 (目录树)，最后才加入文件的实际
# 档名与路径 (kernel 与 initrd)；鸟哥的/boot为/dev/hda1 :
		root    (hd0,0)          <==代表核心文件放在那个 partition 当中
		kernel  /vmlinuz-2.6.18-92.el5 ro root=LABEL=/1 rhgb quiet
		initrd  /initrd-2.6.18-92.el5.img
		
# 2. 直接指定 partition 与档名，不需要额外指定核心文件所在装置代号
		kernel  (hd0,0)/vmlinuz-2.6.18-92.el5 ro root=LABEL=/1 rhgb quiet
		initrd  (hd0,0)/initrd-2.6.18-92.el5.img     
```

**启动时利用 chain loader 的方式转交控制权**

所谓的 chain loader (启动管理程序的链结) 仅是在将控制权交给下一个 boot loader 而已， 所以 grub 并不需要认识与找出 kernel 的档名 ，『 他只是将 boot 的控制权交给下一个 boot sector 或 MBR 内的 boot loader 而已 』 所以通常他也不需要去查验下一个 boot loader 的文件系统

```
[root@www ~]# vi /boot/grub/menu.lst
....前略....
title Windows partition
	rootnoverify (hd0,0)   <==不检验此分割槽
	chainloader +1
	makeactive             <==配置此分割槽为启动碟(active)
```
**initrd 的重要性与创建新 initrd 文件**

initrd 可以将 /lib/modules/.... 内的『启动过程当中一定需要的模块』包成一个文件 (档名就是 initrd)， 然后在启动时透过主机的 INT 13 硬件功能将该文件读出来解压缩，并且 initrd 在内存内会模拟成为根目录， 由於此虚拟文件系统主要包含磁碟与文件系统的模块，因此我们的核心最后就能够认识实际的磁碟， 那就能够进行实际根目录的挂载啦！  

所以说：『initrd 内所包含的模块大多是与启动过程有关，而主要以文件系统及硬盘模块 (如 usb, SCSI 等) 为主』的！

若想要重建 initrd ，可使用 `mkinitrd` 处理

**测试与安装 grub**: 

`grub-install` 可以安装配置档 (包括文件系统定义档与 menu.lst 等等) ， 即stage2 ！   
如果要将 grub 的 stage1 主程序安装起来，就得要使用`grub shell`的功能。

**[为个别菜单加上口令:  grub-md5-crypt](http://vbird.dic.ksu.edu.tw/linux_basic/0510osloader.php#grub_password)**

##### 4. [启动过程的问题解决](http://vbird.dic.ksu.edu.tw/linux_basic/0510osloader.php#solution)  
**[忘记 root 口令的解决之道](http://vbird.dic.ksu.edu.tw/linux_basic/0510osloader.php#solution_root)**  
**[init 配置档错误](http://vbird.dic.ksu.edu.tw/linux_basic/0510osloader.php#solution_init)**  
**[BIOS 磁碟对应的问题 (device.map)](http://vbird.dic.ksu.edu.tw/linux_basic/0510osloader.php#solution_hd)**  
**[因文件系统错误而无法启动](http://vbird.dic.ksu.edu.tw/linux_basic/0510osloader.php#solution_config)**  
**[利用 chroot 切换到另一颗硬盘工作](http://vbird.dic.ksu.edu.tw/linux_basic/0510osloader.php#solution_chroot)**
