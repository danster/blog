# 鸟哥的Linux私房菜基础篇_下 -- 软件安装、Linux备份、核心编译  

<!-- toc -->

### [第二十二章、软件安装：原始码与 Tarball](http://vbird.dic.ksu.edu.tw/linux_basic/0520source_code_and_tarball.php)

Linux 系统上真正认识的可运行档其实是二进位文件 (binary program)，例如 /usr/bin/passwd, /bin/touch

>  binary 而且是可以运行的时候，他就会显示运行档类别 (ELF 32-bit LSB executable)， 同时会说明是否使用动态函式库 (shared libs)，而如果是一般的 script ，那他就会显示出 text executables 之类的字样

```
# 先以系统的文件测试看看：
[root@www ~]# file /bin/bash
/bin/bash: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), 
for GNU/ Linux 2.6.9, dynamically linked (uses shared libs), 
for GNU/Linux 2.6.9, stripped

# 如果是系统提供的 /etc/init.d/syslog 呢？
[root@www ~]# file /etc/init.d/syslog
/etc/init.d/syslog: Bourne-Again shell script text executable

# 如果script中把 #!/bin/bash 这第一行拿掉，那么不管script的权限为何，
# 他其实显示的是 ASCII 文字档
```

所谓的 Tarball 文件，其实就是将软件的所有原始码文件先以 tar 打包，然后再以压缩技术来压缩，如 *.tar.gz *.tgz  *.tar.bz2 之类，所以说， Tarball 是一个软件包， 你将他解压缩之后，里面的文件通常就会有：

- 原始程序码文件；
- 侦测程序文件 (可能是 configure 或 config 等档名)；
- 本软件的简易说明与安装说明 (INSTALL 或 README)。

侦测程序文件(./configure) 侦测使用者的作业环境， 以及该作业环境是否有软件开发商所需要的其他功能，该侦测程序侦测完毕后，就会主动的创建 Makefile 文件. 一般来说，侦测程序会侦测的数据大约有底下这些：

- 是否有适合的编译器可以编译本软件的程序码；
- 是否已经存在本软件所需要的函式库，或其他需要的相依软件；
- 操作系统平台是否适合本软件，包括 Linux 的核心版本；
- 核心的表头定义档 (header include) 是否存在 (驱动程序必须要的侦测)。

基本上软件安装与升级的方法可以分为两大类，分别是：
> 直接以原始码透过编译来安装与升级；  
> 直接以编译好的 binary program 来安装与升级。

Linux distribution 厂商能够针对自己的作业平台先进行编译等过程，再将编译好的 binary program 释出的话，那由於我的系统与该 Linux distribution 的环境是相同的，所以他所释出的 binary program 就可以在我的机器上面直接安装啦！省略了侦测与编译等等繁杂的过程呢！

这个预先编译好程序的机制存在於很多 distribution 喔，包括有 Red Hat 系统 (含 Fedora/CentOS 系列) 发展的 **RPM** 软件管理机制与 **yum** 线上升级模式； Debian 使用的 **dpkg** 软件管理机制与 **apt** 线上升级模式等等。如： CentOS 依循标准的 Linux distribution，Tarball 和 RPM 都可以用来安装与升级。

大部分的 Tarball 软件之安装的命令下达方式

```
0 取得原始档：将 tarball 文件在 /usr/local/src 目录下解压缩；
1 ./configure ：创建 Makefile 文件， 这个步骤的相关资讯应该要参考一下该目录下的
				README 或 INSTALL 相关的文件
2 make clean ：去除可能的中间文件和目标文件
3 make ： 将原始码编译成为可以被运行的可运行档，而这个可运行档会放置在
          目前所在的目录之下， 尚未被安装到预定安装的目录中
4 make install ：将上一步骤所编译完成的数据安装到预定的目录中，完成安装
5 如果安装成功，例如安装到 /usr/local/packages 这个目录中，还必需手动的将这个
  软件的 man page 写入 /etc/man.config 里面去, 写下: 
  MANPATH /usr/local/software/man

# 上面的步骤是一步一步来进行的，而其中只要一个步骤无法成功，
# 那么后续的步骤就完全没有办法进行的
```
**gcc 的简易用法** (编译、参数与链结)

```
# 仅将原始码编译成为目标档，并不制作连结等功能：
[root@www ~]# gcc -c hello.c
# 会自动的产生 hello.o 这个文件，但是并不会产生 binary 运行档。

# 在编译的时候，依据作业环境给予最佳化运行速度
[root@www ~]# gcc -O hello.c -c
# 会自动的产生 hello.o 这个文件，并且进行最佳化喔！

# 在进行 binary file 制作时，将连结的函式库与相关的路径填入
[root@www ~]# gcc sin.c -lm -L/usr/lib -I/usr/include
# 这个命令较常下达在最终连结成 binary file 的时候，
# -lm 指的是 libm.so 或 libm.a 这个函式库文件；
# -L 后面接的路径是刚刚上面那个函式库的搜寻目录；
# -I 后面接的是原始码内的 include 文件之所在目录。

# 将编译的结果输出成某个特定档名
[root@www ~]# gcc -o hello hello.c
# -o 后面接的是要输出的 binary file 档名

# 在编译的时候，输出较多的信息说明
[root@www ~]# gcc -o hello hello.c -Wall
# 加入 -Wall 之后，程序的编译会变的较为严谨一点，
# 所以警告信息也会显示出来！

# 通常称 -Wall 或者 -O 这些非必要的参数为旗标 (FLAGS)，
# 因为使用的是 C，所以简称CFLAGS 
```

make 的好处：

```
- 简化编译时所需要下达的命令；
- 若在编译完成之后，修改了某个原始码文件，则 make 仅会针对
  被修改了的文件进行编译，其他的 object file 不会被更动；
- 最后可以依照相依性来升级 (update) 运行档。
```

基本的 makefile 守则是这样的：

```
标的(target): 目标档1 目标档2
<tab>   gcc -o 欲创建的运行档 目标档1 目标档2
```

- 在 makefile 当中的 # 代表注解；
- <tab> 需要在命令行 (例如 gcc 这个编译器命令) 的第一个字节；
- 标的 (target) 与相依文件(就是目标档)之间需以『:』隔开。

make 中的变量

```
[root@www ~]# vi makefile
LIBS = -lm
OBJS = main.o haha.o sin_value.o cos_value.o
main: ${OBJS}
		gcc -o $@ ${OBJS} ${LIBS}   <==那个 $@ 就是 main ！
clean:
		rm -f main ${OBJS}
```
与 bash shell script 的语法有点不太相同，变量的基本语法为：

- 变量与变量内容以『=』隔开，同时两边可以具有空格；
- 变量左边不可以有 <tab> ，例如上面范例的第一行 LIBS 左边不可以是 <tab>；
- 变量与变量内容在『=』两边不能具有『:』；
- 在习惯上，变量最好是以『大写字母』为主；
- 运用变量时，以 ${变量} 或 $(变量) 使用；
- 在该 shell 的环境变量是可以被套用的，例如提到的 CFLAGS 这个变量！
- 在命令列模式也可以给予变量。
- 特殊的变量：$@：代表目前的标的(target)

make中的环境变量取用的守则是这样的：

- make 命令列后面加上的环境变量为优先；
- makefile 里面指定的环境变量第二；
- shell 原本具有的环境变量第三。

**Tarball 安装注意事项**

Linux distribution 默认的安装软件的路径(以rpm安装apache为例)：  

```
/etc/httpd  # 配置档
/usr/lib  # 函式库
/usr/bin  # 运行档
/usr/share/doc	# 一些基本的软件使用手册与说明档
/usr/share/man  # 线上说明档
```
以 tarball 来安装时呢？如果是放在默认的 /usr/local 里面：

```
/usr/local/etc
/usr/local/bin
/usr/local/lib
/usr/local/man

# 如果你都安装在这个目录下的话，那么未来再想要升级或移除的时候，
# 就会比较难以追查文件的来源
```

如果在安装的时候选择的是单独的目录:

```
/usr/local/apache/etc
/usr/local/apache/bin
/usr/local/apache/lib
/usr/local/apache/man

# 单一软件的文件都在同一个目录之下，那么要移除该软件就简单的多了！ 
# 只要将该目录移除即可视为该软件已经被移除: rm -rf /usr/local/apache

# 但是要重新配置：PATH, MANPATH 等
# 除此之外，Tarball 在升级的时候也很困扰
```

方便 Tarball 的管理，通常鸟哥会这样建议使用者：

- 最好将 tarball 的原始数据解压缩到 /usr/local/src 当中；
- 安装时，最好安装到 /usr/local 这个默认路径下；
- 考虑未来的反安装步骤，最好可以将每个软件单独的安装在 /usr/local 底下；
- 为安装到单独目录的软件之 man page 加入 man path 搜寻

**利用 patch 升级原始码**

**diff**，这个命令可以将『两个文件之间的差异性列出来』呢！那我们也知道新旧版本的文件之间， 其实只有修改一些程序码而已，那么我们可以透过 diff 比对出新旧版本之间的文字差异，然后再以相关的命令来将旧版的文件升级吗？ 

当然可以啦！那就是 **patch** 这个命令啦！很多的软件开发商在升级了原始码之后，几乎都会释出所谓的 patch file，也就是直接将原始码 update 而已的一个方式喔！

#### 函式库管理

**静态函式库(libxxx.a)**的特色：

- 编译行为：这类函式库在编译的时候会直接整合到运行程序当中，所以利用静态函式库编译成的文件会比较大一些喔；
- 独立运行的状态：这类函式库最大的优点，就是编译成功的可运行档可以独立运行，而不需要再向外部要求读取函式库的内容 (请参照动态函式库的说明)。
- 升级难易度：虽然运行档可以独立运行，但因为函式库是直接整合到运行档中， 因此若函式库升级时，整个运行档必须要重新编译才能将新版的函式库整合到程序当中。 也就是说，在升级方面，只要函式库升级了，所有将此函式库纳入的程序都需要重新编译！

**动态函式库(libxxx.so)**的特色:

- 编译行为：动态函式库与静态函式库的编译行为差异挺大的。 与静态函式库被整个捉到程序中不同的，动态函式库在编译的时候，在程序里面只有一个『指向 (Pointer)』的位置而已。也就是说，动态函式库的内容并没有被整合到运行档当中，而是当运行档要使用到函式库的机制时， 程序才会去读取函式库来使用。由於运行档当中仅具有指向动态函式库所在的指标而已， 并不包含函式库的内容，所以他的文件会比较小一点。

- 独立运行的状态：这类型的函式库所编译出来的程序不能被独立运行， 因为当我们使用到函式库的机制时，程序才会去读取函式库，所以函式库文件『必须要存在』才行，而且，函式库的『所在目录也不能改变』，因为我们的可运行档里面仅有『指标』亦即当要取用该动态函式库时， 程序会主动去某个路径下读取，呵呵！所以动态函式库可不能随意移动或删除，会影响很多相依的程序软件喔！

- 升级难易度：虽然这类型的运行档无法独立运行，然而由於是具有指向的功能， 所以，当函式库升级后，运行档根本不需要进行重新编译的行为，因为运行档会直接指向新的函式库文件 (前提是函式库新旧版本的档名相同喔！)。

目前的 Linux distribution 比较倾向於使用动态函式库，因为函式库的升级更方便，绝大多数的函式库都放置在：/usr/lib, /lib 目录下，Linux 系统里面很多的 kernel 的函式库放在 /lib/modules 里面。

**ldconfig**：如果我们将常用的动态函式库先加载内存当中(缓存, cache)，当软件要取用动态函式库时，就可以增进动态函式库的读取速度.

1. 首先，我们必须要在 /etc/ld.so.conf 里面写下『 想要读入高速缓存当中的动态函式库所在的目录』，注意喔， 是目录而不是文件；
2. 接下来则是利用 ldconfig 命令将 /etc/ld.so.conf 的数据读入缓存当中；
3. 同时也将数据记录一份在 /etc/ld.so.cache 这个文件当中呐！

**ldconfig** 还可以用来判断动态函式库的连结资讯

```
[root@www ~]# ldconfig [-f conf] [ -C cache] [-p]
选项与参数：
-f conf ：那个 conf 指的是某个文件名称，也就是说，使用 conf 作为 libarary 
	      函式库的取得路径，而不以 /etc/ld.so.conf 为默认值
-C cache：那个 cache 指的是某个文件名称，也就是说，使用 cache 作为缓存缓存
	      的函式库数据，而不以 /etc/ld.so.cache 为默认值
-p	：列出目前有的所有函式库数据内容 (在 /etc/ld.so.cache 内的数据！)

[root@www ~]# vi /etc/ld.so.conf #假设MySQL函式库在/usr/lib/mysql
include ld.so.conf.d/*.conf
/usr/lib/mysql   <==这一行新增的啦！

[root@www ~]# ldconfig  <==画面上不会显示任何的资讯
[root@www ~]# ldconfig -p
530 libs found in cache `/etc/ld.so.cache'
        libz.so.1 (libc6) => /usr/lib/libz.so.1
        libxslt.so.1 (libc6) => /usr/lib/libxslt.so.1
		.......
#       函式库名称 => 该函式库实际路径
```

**ldd**: 判断某个可运行的 binary 文件含有什么动态函式库, 还可以视察『相依函式库』之间的相关性

```
[root@www ~]# ldd [-vdr] [filename]
选项与参数：
-v ：列出所有内容资讯；
-d ：重新将数据有遗失的 link 点秀出来！
-r ：将 ELF 有关的错误内容秀出来！

范例一：找出 /usr/bin/passwd(有使用到 pam 模块)这个文件的函式库数据
[root@www ~]# ldd /usr/bin/passwd
....(....
        libaudit.so.0 => /lib/libaudit.so.0 (0x00494000)  <==SELinux
        libselinux.so.1 => /lib/libselinux.so.1 (0x00101000) <==SELinux
        libc.so.6 => /lib/libc.so.6 (0x00b99000)
        libpam.so.0 => /lib/libpam.so.0 (0x004ab000)    <==PAM 模块
		........

范例二：找出 /lib/libc.so.6 这个函式的相关其他函式库！
[root@www ~]# ldd -v /lib/libc.so.6
        /lib/ld-linux.so.2 (0x00ab3000)
        linux-gate.so.1 =>  (0x00636000)

        Version information:  <==使用 -v 选项，添加显示其他版本资讯！
        /lib/libc.so.6:
                ld-linux.so.2 (GLIBC_PRIVATE) => /lib/ld-linux.so.2
                ld-linux.so.2 (GLIBC_2.3) => /lib/ld-linux.so.2
                ld-linux.so.2 (GLIBC_2.1) => /lib/ld-linux.so.2
```

检验软件正确性

```
md5sum/sha1sum filename # 以 md5sum 与 sha1sum 去检验文件，回传指纹码
```

###<a name=23>[第二十三章、软件安装：RPM，SRPM 与 YUM 功能](http://vbird.dic.ksu.edu.tw/linux_basic/0520rpm_and_srpm.php)</a>

Linux Distribution : 

1. Linux 开发商先在固定的硬件平台与操作系统平台上面将需要安装或升级的软件编译好， 然后将这个软件的所有相关文件打包成为一个特殊格式的文件，在这个软件文件内还包含了**预先侦测系统与相依软件的脚本**，并提供记载该软件提供的所有文件资讯等。最终将这个软件文件释出.
2. 用户端取得这个文件后，只要透过特定的命令来安装， 那么该软件文件就会**依照内部的脚本来侦测相依的前驱软件是否存在**，若安装的环境符合需求，那就会**开始安装**，安装完成后还会**将该软件的资讯写入软件管理机制**中，以达成未来可以进行升级、移除等动作

目前在 Linux 界软件安装方式最常见的有两种: dpkg, rpm

- dpkg: 这个机制最早是由 Debian Linux 社群所开发, 基于或衍生于Debian的 Linux distributions 有 : Debian, B2D, Ubuntu 等
- rpm： RedHat Package Manager, 最早由Red Hat公司开发，包括：Fedora, CentOS, SuSE 等都在使用

**线上升级机制**：不论 dpkg/rpm 这些机制或多或少都会有软件属性相依的问题，我们将相依属性的数据做成列表， 等到实际软件安装时，若发生有相依属性的软件状况时，例如安装 A 需要先安装 B 与 C ，而安装 B 则需要安装 D 与 E 时，那么当你要安装 A ，透过相依属性列表，管理机制自动去取得 B, C, D, E 来同时安装， 就解决了属性相依的问题。

dpkg 管理机制上就开发出 APT (apt-get) 的线上升级机制，RPM 则依开发商的不同，有 Red Hat 系统的 yum ， SuSE 系统的 Yast Online Update (YOU)， Mandriva 的 urpmi 软件等

|distribution 代表 |	软件管理机制(命令)| 线上升级机制(命令) |
|---|---|---|
|Red Hat/Fedora	|RPM(rpm, rpmbuild)	|YUM (yum)|
|Debian/Ubuntu	|DPKG(dpkg) |APT (apt-get)|

软件管理机制的问题是：

- 软件文件安装的环境必须与打包时的环境需求一致或相当；
- 需要满足软件的相依属性需求；
- 反安装时需要特别小心，最底层的软件不可先移除，否则可能造成整个系统的问题！

**SRPM**(***.src.rpm): Source RPM, 也就是这个 RPM 文件里面含有原始码. SRPM 虽然内容是原始码， 但是他仍然含有该软件所需要的相依性软件说明、以及所有 RPM 文件所提供的数据。同时，他与 RPM 不同的是，他也提供了参数配置档 (就是 configure 与 makefile), 我们可以透过修改 SRPM 内的参数配置档，然后重新编译产生能适合我们 Linux 环境的 RPM 文件. 

所以要安装SRPM，你必须：

- 先将该软件以 RPM 管理的方式编译，此时 SRPM 会被编译成为 RPM 文件；
- 然后将编译完成的 RPM 文件安装到 Linux 系统当中

```
# 文件的意义
rp-pppoe-3.1-5.i386.rpm
rp-pppoe  -3.1          -5              .i386        .rpm
软件名称    软件的版本资讯  释出(编译)的次数  适合的硬件平台 扩展名
```

释出版本次数：由于同一版的软件中，可能由于有某些 bug 或者是安全上的顾虑，必须要进行小幅度的 patch 或重设一些编译参数，配置完成之后重新编译并打包成 RPM 文件！因此就有不同的打包数出现了.

目前CPU大多是64位的等级，因此你可以在该硬件上面安装x86\_64或i386等级的RPM软件。但在你的旧型主机，例如 P-III/P-4 32 位机器上面，就不能够安装 x86_64 的软件。

RPM 属性相依问题： RPM 会去检验一下数据库里面是否已经存在相关的软件了，如果数据库显示不存在，那么这个 RPM 文件『默认』就不能安装。  
RPM 属性相依的克服方式： **YUM** 线上升级：在有要安装软件需求的时候，先到将这些相依属性的软件先列表去找，同时与系统内已安装的软件相比较，没安装到的相依软件就一口气同时安装起来

为了重复利用既有的软件功能，因此很多软件都会以函式库的方式释出部分功能，以方便其他软件的呼叫应用， 例如 PAM 模块的验证功能

RPM 文件安装完毕后，该软件相关的资讯就会被写入 /var/lib/rpm/ 目录下的数据库文件中，我们有任何软件升级的需求，版本之间的比较就是来自於这个数据库， 而如果你想要查询系统已经安装的软件，也是从这里查询的。

```shell
[root@www ~]# rpm -qa                          <==已安装软件
[root@www ~]# rpm -q[licdR] 已安装的软件名称      <==已安装软件
[root@www ~]# rpm -qf 存在於系统上面的某个档名     <==已安装软件
[root@www ~]# rpm -qp[licdR] 未安装的某个文件名称  <==查阅RPM文件
选项与参数：
查询已安装软件的资讯：
-q  ：仅查询，后面接的软件名称是否有安装；
-qa ：列出所有的，已经安装在本机 Linux 系统上面的所有软件名称；
-qi ：列出该软件的详细资讯 (information)，包含开发商、版本与说明等；
-ql ：列出该软件所有的文件与目录所在完整档名 (list)；
-qc ：列出该软件的所有配置档 (找出在 /etc/ 底下的档名而已)
-qd ：列出该软件的所有说明档 (找出与 man 有关的文件而已)
-qR ：列出与该软件有关的相依软件所含的文件 (Required 的意思)
-qf ：由后面接的文件名称，找出该文件属於哪一个已安装的软件；
查询某个 RPM 文件内含有的资讯：
-qp[icdlR]：注意 -qp 后面接的所有参数以上面的说明一致。但用途仅在於找出
	    某个 RPM 文件内的资讯，而非已安装的软件资讯！注意！  

[root@www ~]# rpm -Va
[root@www ~]# rpm -V  已安装的软件名称
[root@www ~]# rpm -Vp 某个 RPM 文件的档名
[root@www ~]# rpm -Vf 在系统上面的某个文件
选项与参数：
-V  ：后面加的是软件名称，若该软件所含的文件被更动过，才会列出来；
-Va ：列出目前系统上面所有可能被更动过的文件；
-Vp ：后面加的是文件名称，列出该软件内可能被更动过的文件；
-Vf ：列出某个文件是否被更动过～
[root@www ~]# rpm --rebuilddb   <==重建数据库
```

知道到底我的文件被更动过的内容:

```
[root@www ~]# rpm -Vf /etc/crontab
S.5....T  c /etc/crontab
# 瞧！因为有被更动过，所以会列出被更动过的资讯类型！
```
c 代表的是『 Config file 』的意思，也就是文件的类型，文件类型有底下这几类：

- c ：配置档 (config file)
- d ：文件数据档 (documentation)
- g ：鬼文件～通常是该文件不被某个软件所包含，较少发生！(ghost file)
- l ：授权文件 (license file)
- r ：读我文件 (read me)

至於最前面的八个资讯是：

- S ：(file Size differs) 文件的容量大小是否被改变
- M ：(Mode differs) 文件的类型或文件的属性 (rwx) 是否被改变？如是否可运行等参数已被改变
- 5 ：(MD5 sum differs) MD5 这一种指纹码的内容已经不同
- D ：(Device major/minor number mis-match) 装置的主/次代码已经改变
- L ：(readLink(2) path mis-match) Link 路径已被改变
- U ：(User ownership differs) 文件的所属人已被改变
- G ：(Group ownership differs) 文件的所属群组已被改变
- T ：(mTime differs) 文件的创建时间已被改变

配置 yum 服务器所提供的容器，一般高速网络中心也提供了 addons, centosplus, extras, fasttrack, os(系统默认的软件), updates(软件升级版本) 等容器。

让我们修改配置档吧：

```
[root@www ~]# vi /etc/yum.repos.d/CentOS-Base.repo
[base] # 代表容器的名字
name=CentOS-$releasever - Base  # 只是说明一下这个容器的意义
baseurl=http://ftp.twaren.net/Linux/CentOS/5/os/i386/ #最重要，容器的实际网址
gpgcheck=1 # 指定是否需要查阅 RPM 文件内的数码签章
gpgkey=http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-5  #数码签章的公钥档所在位置

# 底下其他的容器项目，请自行到高速网络中心去查询后自己处理！
```

```
# 列出目前 yum server 所使用的容器有哪些？
[root@www ~]# yum repolist all
repo id         repo name                  status
addons          CentOS-5 - Addons          enabled
base            CentOS-5 - Base            enabled
c5-media        CentOS-5 - Media           disabled
centosplus      CentOS-5 - Plus            disabled
extras          CentOS-5 - Extras          enabled
updates         CentOS-5 - Updates         enabled
```

### [第二十五章、Linux 备份策略](http://vbird.dic.ksu.edu.tw/linux_basic/0580backup.php)

**哪些 Linux 数据具有备份的意义**

操作系统本身需要备份的文件

```
/etc/ 整个目录
/home/ 整个目录
/var/spool/mail
/boot
/root
/usr/local/ 
/opt/
```

网络服务的数据库方面

```
软件本身的配置文件，例如：/etc/ 整个目录，/usr/local/ 整个目录
软件服务提供的数据，以 WWW 及 MySQL 为例：
WWW 数据：/var/www 整个目录或 /srv/www 整个目录，及系统的使用者家目录
MySQL ： /var/lib/mysql 整个目录
其他在 Linux 主机上面提供的服务之数据库文件！
```

推荐需要备份的目录

```
如果你的硬件或者是由於经费的关系而无法全部的数据都予以备份时， 
鸟哥建议你至少需要备份这些目录！

/boot
/etc
/home
/root
/usr/local(或者是 /opt 及 /srv 等)
/var(注：这个目录当中有些缓存目录则可以不备份！)
```
不需要备份的目录：

```
/dev ：这个随便你要不要备份
/proc：这个真的不需要备份啦！
/mnt 与 /media：如果你没有在这个目录内放置你自己系统的东西，也不需要备份
/tmp ：干嘛存缓存档！不需要备份！
```
#### 备份的种类、频率与工具的选择

还原的考量: 完整备份 还是 部分备份 还是仅仅备份关键数据

累积备份的原则: 系统在进行完第一次完整备份后，经过一段时间的运行， 比较系统与备份档之间的差异，仅备份有差异的文件而已。而第二次累积备份则与第一次累积备份的数据比较， 也是仅备份有差异的数据而已。


完整备份常用的工具有 dd, cpio, dump/restore 等等，因为这些工具都能够备份装置与特殊文件。 
  
- dd 可以直接读取磁碟的磁区 (sector) 而不理会文件系统
- cpio 是能够备份所有档名，不过，得要配合 find 或其他找档名的命令才能够处理妥当。

备份关键数据鸟哥最爱使用 tar 来处理了！如果分门别类的使用不同档名，配合 date 命令是非常好用的工具.

```
# 用 dd 来将 /dev/sda 备份到完全一模一样的 /dev/sdb 硬盘上：
[root@www ~]# dd if=/dev/sda of=/dev/sdb
# 由於 dd 是读取磁区，所以 /dev/sdb 这颗磁碟可以不必格式化！非常的方便！
# 只是你会等非常非常久！因为 dd 的速度比较慢！

# 使用 cpio 来备份与还原整个系统，假设储存媒体为 SATA 磁带机：
[root@www ~]# find / -print | cpio -covB > /dev/st0  <==备份到磁带机
[root@www ~]# cpio -iduv < /dev/st0                  <==还原

# 完整备份
[root@www ~]# dump -0u -f /backupdata/home.dump /home
# 第一次进行累积备份
[root@www ~]# dump -1u -f /backupdata/home.dump.1 /home

# 用 tar 依据日期来备份 mysql 的数据库
[root@www ~]# tar -jpcvf mysql.`date +%Y-%m-%d`.tar.bz2 /var/lib/mysql
```

鸟哥对鸟站的备份策略：

```
主机硬件：使用一个独立的 filesystem 来储存备份数据，此 filesystem 挂载到 /backup 当中；
每日进行：目前仅备份 MySQL 数据库；
每周进行：包括 /home, /var, /etc, /boot, /usr/local 等目录与特殊服务的目录；
自动处理：这方面利用 /etc/crontab 来自动提供备份的进行；
异地备援：每月定期的将数据分别 (a)烧录到光盘上面 (b)使用网络传输到另一部机器上面。
```

###<a name=26>[第二十六章、核心编译](http://vbird.dic.ksu.edu.tw/linux_basic/0540kernel.php)</a>

核心是什么？ 核心文件是什么？
> **核心**就是系统上面的一个文件而已, 是透过原始码编译而成的, 它包含了驱动主机各项硬件的侦测程序与驱动模块 
> 
> 在启动流程分析中，当系统读完 BIOS 并加载 MBR 内的启动管理程序后，就能够加载**核心**到内存当中。然后**核心**开始侦测硬件，挂载根目录并取得**核心模块**来驱动所有的硬件，之后呼叫 /sbin/init 就能够依序启动所有系统所需要的服务了
> 
> **核心文件**通常被放置成 /boot/vmlinuz, 不过也不见得, 因为一部主机上面可以拥有多个核心文件，只是启动的时候仅能选择一个来加载而已. 甚至我们也可以在一个 distribution 上面放置多个核心，然后以这些核心来做成多重启动.

核心模块是什么？ 
>**核心文件**已经包含了硬件侦测与驱动模块等重要模块，但还有一些不常用的类似驱动程序的咚咚独立出核心文件，编译成为独立的**核心模块**，然后核心可以在系统正常运行的过程当中加载这些模块到**核心**。  
>如此一来， 我在不需要更动核心的前提之下，只要编译出适当的**核心模块**，并且加载他， Linux 就可以使用这些硬件啦。

核心的主要工作是在控制硬件，而且是『越简单越好』

**编译核心**的可能目的：

- 新功能的需求: 比如新的功能只有在新的核心里面才有
- 原本核心太过臃肿: 重新编译核心来取消掉很多莫名其妙的功能
- 与硬件搭配的稳定性: 比如CPU由Intel变成AMD
- 其他需求 (如嵌入式系统): 比如由于需要较为小而美的系统，核心就需要更简洁有力

> 重新编译核心虽然可以针对你的硬件作最佳化的步骤 (例如CPU问题) ，不过由於这些最佳化的步骤对於整体效能的影响是很小很小的， 因此如果是为了添加效能来编译核心的话，基本上，效益不大！  
> 然而，如果是针对『系统稳定性』来考量的话， 那么就有充分的理由来重新编译核心

核心的版本

> 主要的版本定义为: [主].[次].[释出]-[修改] 的样式, 2.6.x 是稳定版本, 2.5.x 是测试版本. 重新编译核心时，大多使用偶数的核心版本.  
>  2.4.x 与 2.6.x 是两个相当大差异的核心版本, 2.4.xx 可以升级到 2.4.xx, 但是不要由 2.4.xx 直接升级到 2.6.xx 

####核心原始码的解压缩/安装/观察

```
[root@www ~]# wget ftp://linux.cis.nctu.edu.tw/kernel/linux/kernel/v2.6/linux-2.6.30.3.tar.bz2
[root@www ~]# tar -jxvf linux-2.6.30.3.tar.bz2 -C /usr/src/kernels/
[root@www ~]# cd /usr/src/kernels/linux-2.6.30.3
[root@www ~]# ll
# 核心目录下含有哪些重要数据呢？基本上有底下这些东西：

arch ：与硬件平台有关的项目，大部分指的是 CPU 的类别，例如 x86, x86_64, Xen 虚拟支持等；
block ：与区块装置较相关的配置数据，区块数据通常指的是大量储存媒体！还包括类似 ext3 等文件系统的支持是否允许等。
crypto ：核心所支持的加密的技术，例如 md5 或者是 des 等等；
Documentation ：与核心有关的一堆说明文件，若对核心有极大的兴趣，要瞧瞧这里！
drivers ：一些硬件的驱动程序，例如显卡、网络卡、PCI 相关硬件等等；
firmware ：一些旧式硬件的微命令码 (韧体) 数据；
fs ：核心所支持的 filesystems ，例如 vfat, reiserfs, nfs 等等；
include ：一些可让其他程序呼叫的标头 (header) 定义数据；
init ：一些核心初始化的定义功能，包括挂载与 init 程序的呼叫等；
ipc ：定义 Linux 操作系统内各程序的沟通；
kernel ：定义核心的程序、核心状态、运行绪、程序的排程 (schedule)、程序的讯号 (signle) 等
lib ：一些函式库；
mm ：与内存单元有关的各项数据，包括 swap 与虚拟内存等；
net ：与网络有关的各项协议数据，还有防火墙模块 (net/ipv4/netfilter/*) 等等；
security ：包括 selinux 等在内的安全性配置；
sound ：与音效有关的各项模块；
virt ：与虚拟化机器有关的资讯，目前核心支持的是 KVM (Kernel base Virtual Machine)
```
####核心编译的预处理与核心功能选择
预处理

```
[root@www linux-2.6.30.3]# make mrproper
# 这个动作会将以前进行过的核心功能选择文件也删除掉，所以几乎只有第一次运行核心编译前才进行这个动作，
# 其余的时刻，你想要删除前一次编译过程的残留数据，只要下达：

[root@www linux-2.6.30.3]# make clean  
# 仅会删除类似目标档之类的编译过程产生的中间文件，而不会删除配置档
```
开始挑选核心功能： make XXconfig
> /boot/ 底下存在一个名为 config-xxx 的文件, 其实就是核心功能列表档！ 我们底下要进行的动作(make XXconfig)，其实就是作出该文件, 后续小节所要进行的编译动作，其实也就是透过这个文件来处理的.  
> 核心功能的挑选，最后会在 /usr/src/kernels/linux-2.6.30.3/ 底下产生一个名为 .config 的隐藏档， 这个文件就是 /boot/config-xxx 的文件啦！  

.config文件如何创建呢？你可以透过非常多的方法来创建这个文件！常见的方法有：

- make menuconfig : 最常使用的，是文字模式底下可以显示类似图形介面的方式挑选核心功能菜单！
- make oldconfig: 使用已存在的 ./.config 文件内容，使用该文件内的配置值为默认值
- make xconfig: 以Qt为图形介面基础功能的图形化介面显示，需要具有 X window 的支持
- make gconfig: 以 Gtk 为图形介面 ....  X window ...
- make config: 最旧式的功能挑选方法，每个项目都以条列式一条一条的列出让你选择

#### 核心的编译与安装

1.编译核心与核心模块

```
[root@www linux-2.6.30.3]# make help     <==去查阅一下所有可用编译参数
[root@www linux-2.6.30.3]# make clean    <==先清除缓存档
[root@www linux-2.6.30.3]# make bzImage  <==先编译核心
Root device is (8, 1)
Setup is 12696 bytes (padded to 12800 bytes).
System is 2207 kB
CRC 7701ab0e
Kernel: arch/x86/boot/bzImage is ready  (#1)

# 核心(核心文件)已经编译好而且放置在 /usr/src/kernels/linux-2.6.30.3/arch/x86/boot/bzImage 里面了

[root@www linux-2.6.30.3]# make modules  <==再编译模块
```
2.实际安装模块

```
[root@www linux-2.6.30.3]# make modules_install
[root@www linux-2.6.30.3]# ll /lib/modules/
drwxr-xr-x 3 root root 4096  7月 30 14:31 2.6.30.3vbird

[root@www ~]# ll -h /lib/modules/2.6.30.3vbird/
lrwxrwxrwx  1 root root   31  7月 30 14:29 build -> /usr/src/kernels/linux-2.6.30.3
lrwxrwxrwx  1 root root   31  7月 30 14:29 source -> /usr/src/kernels/linux-2.6.30.3
drwxr-xr-x 10 root root 4.0K  7月 30 14:30 kernel
-rw-r--r--  1 root root 337K  7月 30 14:31 modules.alias
-rw-r--r--  1 root root   69  7月 30 14:31 modules.ccwmap
-rw-r--r--  1 root root 224K  7月 30 14:31 modules.dep #记录核心模块的相依属性
# 依据modules.dep，我们可以简单的使用 modprobe 这个命令来加载模块
.......
```
看到没，最终会在 /lib/modules 底下创建起这个核心的相关模块！  
模块这样就已经处理妥当罗～ 接下来，就是准备要进行核心的安装了

3.开始安装新核心与多重核心菜单 (grub)  

移动核心到 /boot 且保留旧核心文件 

```
# 移动实际核心, 不覆盖旧核心
[root@www ~]# cp /usr/.../arch/x86/boot/bzImag /boot/vmlinuz-2.6.30.3vbird  <==实际核心

# 建议配置档也复制备份 
[root@www ~]# cp /usr/.../.config /boot/config-2.6.30.3vbird 
```
创建相对应的 Initial Ram Disk: mkinitrd -v /boot/initrd-2.6.30.3vbird.img  
编辑启动菜单 (grub): vim /boot/grub/menu.lst  
重新以新核心启动、测试(uname -a)、修改

#### 额外(单一)核心模块编译
核心模块依据不同的版本，被分别放置到 /lib/modules/$(uname -r)/kernel/ 目录中，各个硬件的驱动程序则是放置到 /lib/modules/$(uname -r)/kernel/drivers/ 当中

有些驱动程序忘记编译成为模块了，那是否需要重新进行上述的所有动作？   
又如果我想要使用硬件厂商释出的新驱动程序，那该如何是好？

> 解决：去取得原始码后，重新编译成为系统可以加载的模块

硬件开发商提供的额外模块

```
# 1. 将下载的网卡驱动程序解压缩：
[root@www ~]# cd /usr/local/src
[root@www src]# tar -jxvf /root/r8168-8.013.00.tar.bz2 
[root@www src]# cd r8168-8.013.00/

# 2. 开始进行编译与安装：
[root@www r8168-8.013.00]# vi readme  <==注意查一下该文件内容
[root@www r8168-8.013.00]# make clean modules
[root@www r8168-8.013.00]# ll src/*.ko  <==创建底下的模块档！
-rw-r--r-- 1 root root 112216  7月 31 01:11 src/r8168.ko
[root@www r8168-8.013.00]# make install
install -m 744 -c r8168.ko /lib/modules/2.6.30.3vbird/kernel/drivers/net/
# 重点在上面这行！会发现模块已经被移动到核心模块目录！

4. 升级模块相依属性，未来就能够利用 modprobe 来直接取用
[root@www r8168-8.013.00]# depmod -a
```
> 需要提醒的是: 当自行编译模块时， 若你的核心有升级 (例如利用自动升级机制进行线上升级) 时，则你必须要重新编译该模块一次， 重复上面的步骤才行！因为这个模块仅针对目前的核心来编译的啊

利用旧有的核心原始码进行编译

> 如果忘记加入某个模块功能，需要重新编译模块，整个过程非常简单！
>   
> 先到目前的核心原始码所在目录下达 make menuconfig ，然后将 NTFS 的选项配置成为模块，之后直接下达：make fs/ntfs/，ntfs 模块 (ntfs.ko) 就会自动被编译出！
> 
> 然后将该模块复制到 /lib/modules/2.6.30.3vbird/kernel/fs/ntsf/ 目录下， 再运行 depmod -a 
> 
> 这样就可以在原来的核心底下新增某个想要加入的模块功能罗

核心模块管理

- 核心与核心模块是分不开的，驱动程序模块在编译时，更与核心的原始码功能分不开！
- 核心、核心模块、驱动程序模块、核心原始码与标头文件的相关性
- 编译驱动程序的时候老是需要找到核心的原始码才能够顺利编译！ 
- 当核心升级之后，自己之前所编译的核心模块会失效
- 与核心模块有相关的 modprobe 命令，启动时会读取到模块定义数据文件 /etc/modprobe.conf
