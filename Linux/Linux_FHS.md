# Linux 目录与文件
<!-- toc -->
## Filesystem Hierarchy Standard
FHS（Filesystem Hierarchy Standard 文件系统层次结构标准），多数 Linux 版本采用这种文件组织形式，FHS 定义了系统中每个区域的用途、所需要的最小构成的文件和目录同时还给出了例外处理与矛盾处理。 

FHS定义了两层规范，第一层是 `/` 下面的各个目录应该要放什么文件数据，如/etc应该放设置文件，/bin、/sbin 放可执行文件等等。第二层则是针对 `/usr` 及 `/var` 这两个目录的子目录来定义。例如 /var/log 放置系统登录文件、/usr/share 放置共享数据等等。

FHS 是根据以往无数 Linux 用户和开发者的经验总结出来的，并且会维持更新，FHS 依据文件系统使用的频繁与否以及是否允许用户随意改动（注意，不是不能，学习过程中，不要怕这些），将目录定义为四种交互作用的形态，如下所示：

- /usr /opt : shareable, static(不可变的)
- /etc /boot: unshareable, static(不可变的)
- /var/mail /var/news: shareable, variable(可变的)
- /var/run  /var/lock: unshareable, variable(可变的)

```
/bin -> /usr/bin
/lib -> /usr/lib
/lib64 -> /usr/lib64
/sbin -> /usr/sbin
/var/run -> /run
/var/lock -> /run/lock
```

```bash
# ls /
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
# ls /
boot  dev  etc  home  media  mnt  opt  proc  root  run  srv  sys  tmp  usr  var
# ls /usr
bin  etc  games  include  lib  lib64  libexec  sbin  share  src  local tmp -> /var/tmp
# ls /usr/local
bin  etc  games  include  lib  lib64  libexec  sbin  share  src 
# ls /var
adm crash empty gopher lib lock mail opt run tmp cache db games kerberos local log nis preserve  spool  yp 
```

```
/bin: /usr/bin: /usr/local/bin: 可执行二进制文件的目录，如ls、tar、mv、cat等。  
/sbin: /usr/sbin: /usr/local/sbin：放置系统管理员root使用的可执行命令，如fdisk、shutdown、mount等。  
/lib: /usr/lib: /usr/local/lib：系统使用的函数库，程序在执行过程中也需要函数库的协助，重要的有/lib/modules等。 

/boot：放置linux系统启动时用到的一些文件。/boot/vmlinuz为内核文件，以及/boot/gurb。建议单独分区，100M即可
/root：系统管理员root的家目录，系统第一个启动的分区为 /，所以最好将 /root和 /放置在一个分区下。 
/dev：存放linux系统下的设备文件，访问该目录下某个文件，相当于访问某个设备，常用的是挂载光驱 mount /dev/cdrom /mnt。 
/etc：系统配置文件存放的目录，重要的配置文件有 /etc/inittab、/etc/fstab、/etc/init.d、/etc/X11、
      /etc/sysconfig、/etc/xinetd.d修改配置文件之前记得备份。  
/lost+fount：系统异常产生错误时，会将一些遗失的片段放置于此目录下，通常这个目录会自动出现在装置目录下。
      如加载硬盘于 /disk 中，此目录下就会自动产生目录 /disk/lost+found  
/mnt: 用于加载各种文件系统  
/media：用于加载各种媒体，如光盘、软盘，但通常光盘挂载于/mnt/cdrom 下，也不一定，可以选择任意位置进行挂载。  
/opt：给主机额外安装软件所摆放的目录。以前的Linux系统中，习惯放置在 /usr/local 目录下。 
/tmp：一般用户或正在执行的程序临时存放文件的目录，任何人都可以访问，不放重要数据。 
/srv：服务启动之后需要访问的数据目录，如 www 服务需要访问的网页数据存放在 /srv/www 内。

/sys:  此目录数据都在内存中，不占用磁盘空间，用于存放系统信息
/proc：此目录数据都在内存中，存放如系统核心、外部设备、网络状态、进程信息等，
       重要的有/proc/cpuinfo、/proc/interrupts、/proc/dma、/proc/ioports、/proc/net/* 等

/usr：存放应用程序、库文件、各种文档等，建议单独分区，设置较大的磁盘空间。
/usr/bin 存放应用程序，
/usr/share 存放共享数据，
/usr/lib 存放程序运行所必需的一些函数库文件。
/usr/local: 存放软件升级包。
/usr/share/doc: 系统说明文件存放目录
/usr/share/man: 程序说明文件存放目录，使用man ls时会查询/usr/share/man/man1/ls.1.gz 的内容

/var：放置系统执行过程中经常变化的文件, 建议单独分区，设置较大的磁盘空间
/var/log: 随时更改的日志文件
/var/log/message：所有的登录文件存放目录
/var/spool/mail：邮件存放的目录
/var/run: 程序或服务启动后，其PID存放在该目录下
```

```bash
# df
Filesystem                              1K-blocks      Used Available Use% Mounted on
/dev/sda1                                47162880   1595904  45566976   4% /
devtmpfs                                  1931576         0   1931576   0% /dev
tmpfs                                     1941036         0   1941036   0% /dev/shm
tmpfs                                     1941036      8620   1932416   1% /run
tmpfs                                     1941036         0   1941036   0% /sys/fs/cgroup
/dev/sr0                                  7587292   7587292         0 100% /mnt
tmpfs                                      388208         0    388208   0% /run/user/0
# devtmpfs,tmpfs: 虚拟文件系统
```

### /proc
/proc 是一个虚拟文件系统，其由内核控制，并没有承载/proc的设备。因为 /proc 主要存放由内核控制 的状态信息，所以大部分这些信息的逻辑位置位于内核控制的内存。  
对/proc进行一次 'ls -l' 可以看到大部分文件都是0字节大的，不过察看这些文件的时候，确实可以看到一些信息。
这怎么可能？这是因为 /proc 文件系统和其他常规的文件系统一样把自己注册到虚拟文件系统层 (VFS) 了。然而直到当VFS调用它，请求文件、目录的i-node 的时候(如 more、less、cat、apm、free、lspci、top等)，/proc文件系统才根据内核中的信息建立相应的文件和目录。 

/proc 的文件可以用于访问有关内核的状态、计算机的属性、正在运行的进程的状态等信息。大部分 /proc 中的文件和目录提供系统物理环境最新的信息，下面是一些重要的文件：  

```
[root@ex ~]# ls /proc/ --hide=[0-9]* -xF
buddyinfo     bus/        cgroups      cmdline  consoles   cpuinfo   crypto   devices     
diskstats     dma         driver/      execdomains  fb   filesystems  fs/     interrupts  iomem
ioports       irq/        kallsyms     kcore    keys     key-users  kmsg      kpagecount  kpageflags  
loadavg       locks       mdstat       meminfo  misc     modules    mounts@   mtrr        net@
pagetypeinfo  partitions  sched_debug  scsi/    self@    slabinfo   softirqs  stat        swaps     sys/ 
sysrq-trigger sysvipc/ timer_list  timer_stats  tty/     uptime     version   vmallocinfo vmstat    zoneinfo

[root@ex ~]# ls /proc/4462 -xF
attr/   autogroup  auxv   cgroup  clear_refs  cmdline  comm   coredump_filter  cpuset   cwd@   environ   exe@  
fd/  fdinfo/  gid_map   io   limits   loginuid   map_files/   maps   mem   mountinfo   mounts   mountstats   
net/   ns/ numa_maps   oom_adj  oom_score  oom_score_adj  pagemap  personality  projid_map  root@   sched  
sessionid  setgroups  smaps   stack   stat    statm   status   syscall    task/    timers   uid_map   wchan

[root@ex ~]# ll /proc/ --hide=[0-9]*
self -> 32282   mounts -> self/mounts    net -> self/net
[root@ex ~]# ll /proc/4462
cwd  -> /     exe  -> /usr/bin/ovsdb-client  root -> /
```


```
/proc/apm - 高级电源管理（APM）版本信息及电池相关状态信息，通常由apm命令使用
/proc/buddyinfo - 用于诊断内存碎片问题的相关信息文件
/proc/cmdline - 在启动时传递至内核的相关参数信息，这些信息通常由lilo或grub等启动管理工具进行传递
/proc/cpuinfo - CPU 的信息 (型号, 家族, 缓存大小等) 
/proc/crypto - 系统上已安装的内核使用的密码算法及每个算法的详细信息列表
/proc/devices - 系统已经加载的所有块设备和字符设备的信息，包含主设备号和设备组(与主设备号对应的设备类型)名
/proc/diskstats - 每个正在使用且注册的ISA DMA通道的信息列表
/proc/execdomains  - 内核当前支持的执行域（每种操作系统独特“个性”）信息列表； 
/proc/fb - 帧缓冲设备列表文件，包含帧缓冲设备的设备号和相关驱动信息；
/proc/filesystems - 被支持的文件系统,被标示为nodev的文件系统表示不需要块设备的支持,通常mount一个设备时，
                    如果没有指定文件系统类型将通过此文件来决定其所需文件系统的类型； 
/proc/interrupts - X86或X86_64体系架构系统上每个IRQ相关的中断号列表；
                   多路处理器平台上每个CPU对于每个I/O设备均有自己的中断号；
/proc/iomem - 每个物理设备上的记忆体（RAM或者ROM）在系统内存中的映射信息 
/proc/ioports - 当前正在使用且已经注册过的与物理设备进行通讯的输入-输出端口范围信息列表
/proc/kallsyms - 模块管理工具用来动态链接或绑定可装载模块的符号定义，由内核输出, 通常这个文件信息量相当大
/proc/kmsg - 此文件用来保存由内核输出的信息，通常由/sbin/klogd或/bin/dmsg等程序使用
/proc/loadavg - 保存关于CPU和磁盘I/O的负载平均值
/proc/locks - 保存当前由内核锁定的文件的相关信息,包含内核内部的调试数据,每个锁定占据一行且具有惟一的编号
/proc/mdstat - 保存RAID相关的多块磁盘的当前状态信息
/proc/meminfo - 物理内存、交换空间等的信息 
/proc/mounts - 已加载的文件系统的列表
/proc/modules - 已加载的模块, 可以由lsmod命令使用，也可以直接查看
/proc/partitions - 块设备每个分区的主设备号(major)和次设备号(minor)等信息，同时包括每个分区所包含的块(block)数目
/proc/pci proc/bus/pci - 内核初始化时发现的所有PCI设备及其配置信息列表,多为某PCI设备相关IRQ信息,用/sbin/lspci –vb查看
/proc/slabinfo - 在内核中频繁使用的对象(如inode、dentry等)都有自己的cache,即slab pool
/proc/stat - 实时追踪启动以来的统计信息:cpu,intr,ctxt,btime,processes(total_forks),procs_running,procs_blocked 
/proc/swaps - 当前系统上的交换分区及其空间利用信息，如果有多个交换分区的话，则会每个交换分区的信息分别
              存储于/proc/swap目录中的单独文件中,而其优先级数字越低，被使用到的可能性越大；可more
/proc/uptime - 系统上次启动以来的运行时间,第一个数字表示系统运行时间,第二个表示系统空闲时间,单位秒
/proc/version - 内核版本 
/proc/vmstat - 当前系统虚拟内存的多种统计数据，信息量较大，这因系统而有所不同, 可more
/proc/zoneinfo - 内存区域（zone）的详细信息列表，信息量较大, 可more
/proc/21312/  - 进程子目录中包含了可以提供有关进程的状态和环境的重要细节信息的文件。
```

上面讨论的大部分/proc下的文件是只读的，而实际上/proc文件系统可以通过/proc中可读写的文件提供了对内核的交互机制。写这些文件可以改变内核的状态，因而要慎重改动这些文件。  
```
/proc/sys 目录存放所有可读写的文件的目录，可以被用于改变内核属性或行为。
/proc/sys/net/ - 可配置，该目录中文件可以修改机器/网络的网络属性。
/proc/sys/dev/ - 为系统上特殊设备提供参数信息文件的目录，其不同设备的信息文件分别存储于不同的子目录中 
/proc/sys/kernel/ - 可配置，这个目录包含反通用内核行为的信息。
/proc/sys/kernel/{domainname, hostname} - 存放着机器或网络的域名和主机名。
/proc/sys/debug/ - 此目录通常是一空目录
```

`$echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_all` 这将在网络上隐藏你的机器，因为它不响应`icmp_echo`。主机将不会响应其他主机发出的`ping`查询。要改回缺省设置，只要 `$echo 0 > /proc/sys/net/ipv4/icmp_echo_ignore_all`.

**进程子目录**:   
```
# ps -ef | grep 4462
root  4462  4461  /bin/ovsdb-client monitor Interface name,ofport,external_ids --format=json
# ls /proc/4462 -xF
attr/   autogroup  auxv   cgroup  clear_refs  cmdline  comm   coredump_filter  cpuset   cwd@   environ   exe@  fd/  
fdinfo/  gid_map  io   limits   loginuid   map_files/   maps   mem   mountinfo   mounts   mountstats   net/   ns/   
numa_maps   oom_adj  oom_score  oom_score_adj  pagemap  personality  projid_map  root@   sched  sessionid  setgroups 
smaps   stack   stat    statm   status   syscall    task/    timers   uid_map    wchan
```

```
cmdline — 启动当前进程的完整命令，但僵尸进程目录中的此文件不包含任何信息
cwd — 指向当前进程运行目录的一个符号链接
root — 指向当前进程运行根目录的符号链接, Unix/Linux 通常采用chroot命令使每个进程运行于独立的根目录
exe — 指向启动当前进程的可执行文件（完整路径）的符号链接，通过/proc/N/exe可以启动当前进程的一个拷贝
environ — 当前进程的环境变量列表，彼此间用空字符(NULL)隔开；变量用大写字母表示，其值用小写字母表示
fd/ — 该目录包含当前进程打开的每一个文件的文件描述符,这些文件描述符是指向实际文件的一个符号链接
limits — 当前进程所使用的每一个受限资源的软限制、硬限制和管理单元，此文件仅可由实际启动当前进程的UID用户读取
maps — 当前进程关联到的每个可执行文件和库文件在内存中的映射区域及其访问权限所组成的列表
mem — 当前进程所占用的内存空间，由open、read和lseek等系统调用使用，不能被用户读取
stat — 当前进程的状态信息，包含一系统格式化后的数据列，可读性差，通常由ps命令使用
statm — 当前进程占用内存的状态信息，通常以“页面”（page）表示
status — 与stat所提供信息类似，但可读性较好，每行表示一个属性信息；详细请参见 proc的man手册页； 
task/ — 包含由当前进程所运行的每一个线程的相关信息，每个线程的相关信息文件均保存在由线程号(tid)命名的目录中，
        这类似于其内容类似于每个进程目录中的内容
```


### /sys
```bash
[root@ex ~]# ls /sys/ -F
block/  bus/  class/  dev/  devices/  firmware/  fs/  hypervisor/  kernel/  module/  power/
```

> sysfs 挂载于 /sys 目录，sysfs 虚拟文件系统提供了一种比 proc 更为理想的访问内核数据的途径.  
> sysfs 是 Linux 内核中设计较新的一种虚拟的基于内存的文件系统，它的作用与 proc 有些类似，  
> 但除了与 proc 相同的具有查看和设定内核参数功能之外，还有为 Linux 统一设备模型作为管理之用。  
> 相比于 proc 文件系统，使用 sysfs 导出内核数据的方式更为统一，并且组织的方式更好，它的设计从 proc 中吸取了很多教训。

涉及到文件系统实现来说， sysfs 是一种基于 ramfs 实现的内存文件系统，与其它同样以 ramfs 实现的内存文件系统(configfs,debugfs,tmpfs,...)类似， sysfs 也是直接以 VFS 中的 struct inode 和 struct dentry 等 VFS 层次的结构体直接实现文件系统中的各种对象；同时在每个文件系统的私有数据 (如 dentry->d_fsdata 等位置) 上，使用了称为 struct sysfs_dirent 的结构用于表示 /sys 中的每一个目录项。

**sysfs 与 proc**:  
sysfs 与 proc 相比有很多优点，最重要的莫过于设计上的清晰. sysfs 的设计原则是一个属性文件只做一件事情， sysfs 属性文件一般只有一个值，直接读取或写入. 新设计的内核机制应该尽量使用 sysfs 机制，而将 proc 保留给纯净的“进程文件系统”。

### /run

### /var

## 一切皆文件

Linux 对数据文件(.mp3、.bmp), 程序文件(.c、.h、*.o), 设备文件(LCD 触摸屏 鼠标), 网络文件(socket) 等的管理都抽象为文件，使用统一的方式方法管理。

文件分类：

- 普通文件(数据文件)：用于存放数据、程序等信息，一般都长期地存放在外存储器中。普通文件又分为文本文件和二进制文件。
- 目录文件：目录文件是文件系统中一个目录所包含的目录项所组成的文件。
- 设备文件：设备文件是用于为操作系统与设备提供连接的一种文件。在Linux系统中将设备作为文件来处理，操作设备就像是操作普通文件一样。每一个设备对应一个设备文件，存放在 /dev 目录中。
- 链接文件：似于 windows 下的快捷方式，链接又可以分为软链接（符号链接）和硬链接。
- 管道文件：管道文件主要用于在进程间传递数据的一种特殊文件。
- 套接口(socket)文件：主要用于不同计算机间网络通信的一种特殊文件。

## Reference

- [Linux下各个目录的作用及内容](https://cnbin.github.io/blog/2015/06/23/linux-xia-ge-ge-mu-lu-de-zuo-yong-ji-nei-rong/)
- [深入理解linux系统下proc文件系统内容](http://www.cnblogs.com/cute/archive/2011/04/20/2022280.html)  
- [使用 /sys 文件系统访问 Linux 内核](https://www.ibm.com/developerworks/cn/linux/l-cn-sysfs/)
- [使用 /proc 文件系统来访问 Linux 内核的内容](http://www.ibm.com/developerworks/cn/linux/l-proc.html)
