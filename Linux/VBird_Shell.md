##<a name=11>[第十一章、认识与学习 BASH](http://vbird.dic.ksu.edu.tw/linux_basic/0320bash.php)</a>
### Index
- [第十一章、认识与学习 BASH ](#11)
- [第十三章、学习 Shell Scripts ](#13)
### [认识 BASH 这个 Shell](http://vbird.dic.ksu.edu.tw/linux_basic/0320bash.php#bash)
硬件、核心与用户的相关性图示  
![](/images/bash_kernel.jpg)

Linux有多少可以使用的 shells, 可以检查一下 /etc/shells 这个文件.    
为什么我们系统上合法的 shell 要写入 /etc/shells 这个文件啊？这是因为系统某些服务在运行过程中，会去检查使用者能够使用的 shells ，而这些 shell 的查询就是藉由 /etc/shells这个文件啰！

Bash shell 的功能 

- 命令编修能力 (history)
- 命令与文件补全功能： ([tab] 按键的好处)
- 命令别名配置功能： (alias)
- 工作控制、前景背景控制： (job control, foreground, background)
- 程序化脚本： (shell scripts)
- 通配符： (Wildcard)

### [Shell 的变量功能](http://vbird.dic.ksu.edu.tw/linux_basic/0320bash.php#variable)
> 变量就是以一组文字或符号等，来取代一些配置或者是一串保留的数据. 某些特定变量会影响到 bash 的环境: PATH、HOME、MAIL、SHELL 等等

```bash
# version=$(uname -r) # 等号两边不能直接接空格符
# echo $version or ${version}
2.6.32-431.el6.x86_64
[leo@centOS ~]$ version="$version":linux
[leo@centOS ~]$ echo $version
2.6.32-431.el6.x86_64:linux
## 若该变量需要在其他子程序运行, 则需要以 export 来使变量变成环境变量
# export version # 通常大写字符为系统默认变量，自配变量使用小写字符 (纯粹依照嗜好) 
# unset version # 取消变量

## 单引号与双引号的最大不同在于双引号仍然可以保有变量的内容，但单引号内仅能是一般字符 ，而不会有特殊符号
[leo@centOS ~]$ var1='$version'; echo $var1  ------- $version
[leo@centOS ~]$ var2="$version"; echo $var2  ------- 2.6.32-431.el6.x86_64:linux
[leo@centOS ~]$ var3=`$version`; echo $var3  ------- -bash: 2.6.32-431.el6.x86_64:linux: command not found
```

* env : 观察环境变量与常见环境变量说明(PWD=/home/leo)  
* export : 观察环境变量(declare -x PWD="/home/leo")， 还可以将自定义变量转成环境变量
* set : 观察所有变量:含环境变量与自定义变量(version=2.6.32-431.el6.x86_64:linux) 
* locale :  Get locale-specific information

```bash
## set 可以看到 $ ? 
[leo@centOS ~]$ echo $$ --- 13231 # $ 目前这个 shell 所使用的 PID
[leo@centOS ~]$ echo $? --- 0 # ? 刚刚运行完命令的回传值
```
Note: 『环境变量』与『bash 的操作环境』意思不太一样, 举例来说，PS1 并不是环境变量， 但是这个PS1会影响到bash的接口(提示字符嘛)！相关性要厘清喔!

环境变量与自定义变量的差异在于『 该变量是否会被子程序所继续引用』：子程序仅会继承父程序的环境变量，不会继承自定义变量.  
export命令可以分享自己的变量配置给后来呼叫的文件或其他程序.

> 为什么环境变量的数据可以被子程序所引用呢？这是因为内存配置的关系！理论上是这样的： 

当启动一个 shell，操作系统会分配一记忆区块给 shell 使用，此内存内之变量可让子程序取用若在父程序利用 export 功能，可以让自定义变量的内容写到上述的记忆区块当中(环境变量)；  
当加载另一个 shell 时 (亦即启动子程序，而离开原本的父程序了)，子 shell 可以将父 shell的环境变量所在的记忆区块导入自己的环境变量区块当中。

locale -a : Write names of available locales  
基本上，你可以逐一配置每个与语系有关的变量数据，但事实上，如果其他的语系变量都未配置， 且你有配置 LANG 或者是 LC_ALL 时，则其他的语系变量就会被这两个变量所取代！

### [变量键盘读取、数组与宣告： read, array, declare](http://vbird.dic.ksu.edu.tw/linux_basic/0320bash.php#variable_read)

```bash
[root@www ~]# read [-pt] variable
-p  ：后面接提示字符！
-t  ：后面接等待的秒数！！
```

```bash
[root@www ~]# declare [-aixr] variable
-a  ：将后面名为 variable 的变量定义成为数组 (array) 类型
-i  ：将后面名为 variable 的变量定义成为整数数字 (integer) 类型
-x  ：用法与 export 一样，就是将后面的 variable 变成环境变量；
-r  ：将变量配置成为 readonly 类型，该变量不可被更改内容，也不能 unset
-p  ： 可以单独列出变量的类型
+r, +x ：将 - 变成 + 可以进行『取消』动作

[root@www ~]# declare # 将所有的变量名称与内容通通列出来
[root@www ~]# declare -i sum=100+300+50
[root@www ~]# echo $sum
450         <==瞭乎？？
```
```bash
[leo@centOS ~]$ var[1]="person1"; var[2]="person2"; var[3]="person3"
[leo@centOS ~]$ echo ${var}

[leo@centOS ~]$ echo $var

[leo@centOS ~]$ echo ${var[1]},${var[2]},${var[3]} --- person1,person2,person3
```

### [与文件系统及程序的限制关系： ulimit](http://vbird.dic.ksu.edu.tw/linux_basic/0320bash.php#variable_ulimit)

想象一个状况：我的 Linux 主机里面同时登陆了十个人，同时开启了 100 个文件，每个文件的大小约 10MBytes ，请问一下， 我的 Linux 主机的内存要有多大才够？ 10*100*10 = 10000 MBytes = 10GBytes ...老天爷，这样，系统不挂点才有鬼哩！  
为了要预防这个情况的发生，所以我们的 bash 是可以『限制用户的某些系统资源』的，包括可以开启的文件数量， 可以使用的 CPU 时间，可以使用的内存总量等等。如何配置？用 ulimit 吧！

```bash
# man ulimit
ulimit: ulimit [-SHacdefilmnpqrstuvx] [limit] # Modify shell resource limits.
## Provides control over the resources available to the shell and processes 
## it creates, on systems that allow such control.

    Options:
      -S  use the `soft' resource limit, 严格的配置，必定不能超过这个配置的数值；
      -H  use the `hard' resource limit, 警告的配置，可以超过这个配置值，但是若超过则有警告信息。
      -a  all current limits are reported
      -b  the socket buffer size
      -c  the max size of core files created
      -d  the max size of a process's data segment
      -e  the max scheduling priority (`nice')
      -f  the max size of files written by the shell and its children
      -i  the max number of pending signals
      -l  the max size a process may lock into memory
      -m  the max resident set size
      -n  the max number of open file descriptors
      -p  the pipe buffer size
      -q  the max number of bytes in POSIX message queues
      -r  the max real-time scheduling priority
      -s  the max stack size
      -t  the max amount of cpu time in seconds
      -u  the max number of user processes
      -v  the size of virtual memory
      -x  the max number of file locks

    If LIMIT is given, it is the new value of the specified resource; the
    special LIMIT values `soft', `hard', and `unlimited' stand for the
    current soft limit, the current hard limit, and no limit, respectively.
    Otherwise, the current value of the specified resource is printed.  If
    no option is given, then -f is assumed.

    Values are in 1024-byte increments, except for -t, which is in seconds,
    -p, which is in increments of 512 bytes, and -u, which is an unscaled
    number of processes.
```

单一 filesystem 能够支持的单一文件大小与 block 的大小有关。例如 block size 为 1024 byte 时，单一文件可达 16GB 的容量。  
但是，我们可以用 ulimit 来限制使用者可以创建的文件大小喔！ 利用 ulimit -f 就可以来配置了！

```bash
[leo@centOS ~]$ ulimit -a
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 30502
max locked memory       (kbytes, -l) 64
max memory size         (kbytes, -m) unlimited
open files                      (-n) 1024
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 10240
cpu time               (seconds, -t) unlimited
max user processes              (-u) 1024
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited
```

### [变量内容的删除、取代与替换](http://vbird.dic.ksu.edu.tw/linux_basic/0320bash.php#variable_other)

|变量配置方式|	说明|
|------------|------|
|${变量#关键词}<br/>${变量##关键词}|若变量内容从头开始的数据符合『关键词』，则将符合的最短数据删除<br/>若变量内容从头开始的数据符合『关键词』，则将符合的最长数据删除|
|${变量%关键词}<br/>${变量%%关键词}|若变量内容从尾向前的数据符合『关键词』，则将符合的最短数据删除<br/>若变量内容从尾向前的数据符合『关键词』，则将符合的最长数据删除|
|${变量/旧字符串/新字符串}<br/>${变量//旧字符串/新字符串}|若变量内容符合『旧字符串』则『第一个旧字符串会被新字符串取代』<br/>若变量内容符合『旧字符串』则『全部的旧字符串会被新字符串取代』|
|new=${old-content}|判断old变量是否存在，若变量存在则使用既有的配置，若变量不存在则给予new一个新的content|

|变量配置方式|	str 没有配置|	str 为空字符串|	str 已配置非为空字符串|
|------------|--------------|-----------------|-----------------------|
|var=${str-expr}|	var=expr|	var=|	var=$str|
|var=${str:-expr}|	var=expr|	var=expr|	var=$str|
|var=${str+expr}|	var=|	var=expr|	var=expr|
|var=${str:+expr}|	var=|	var=|	var=expr|
|var=${str=expr}|	str=expr;var=expr|	str 不变;var=|	str 不变;var=$str|
|var=${str:=expr}|	str=expr;var=expr|	str=expr;var=expr|	str 不变;var=$str|
|var=${str?expr}|	expr 输出至 stderr|	var=|	var=$str|
|var=${str:?expr}|	expr 输出至 stderr|	expr 输出至 stderr|	var=$str|

> 命令别名与变量有什么不同呢？

命令别名是『新创一个新的命令， 你可以直接下达该命令』的，至于变量则需要使用类似『 echo 』命令才能够呼叫出变量的内容

```bash
[root@www ~]# history
   66  man rm
   67  alias
   68  man history
   69  history 
[root@www ~]# !66  <==运行第 66 笔命令
[root@www ~]# !!   <==运行上一个命令，本例中亦即 !66 
[root@www ~]# !al  <==运行最近以 al 为开头的命令(上头列出的第 67 个)
```
多个bash时，因为要等到注销时才会升级记录文件，所以啰， 最后注销的那个 bash 才会是最后写入数据到~/.bash_history。 如此一来其他 bash 的命令操作就不会被记录下来了 (其实有被记录，只是被后来的最后一个 bash 所覆盖升级了) 。

### [Bash Shell 的操作环境](http://vbird.dic.ksu.edu.tw/linux_basic/0320bash.php#settings)

命令运行的顺序可以这样看：  
1 以相对/绝对路径运行命令，例如『 /bin/ls 』或『 ./ls 』；  
2 由 alias 找到该命令来运行；  
3 由 bash 内建的 (builtin) 命令来运行； 
4 透过 $PATH 这个变量的顺序搜寻到的第一个命令来运行。

bash 的进站与欢迎信息： /etc/issue, /etc/motd, /etc/issue.net (提供给 telnet 远程登录程序用).  
如果您想要让使用者登陆后取得一些信息，例如您想要让大家都知道的信息， 那么可以将信息加入 /etc/motd 里面去！

```bash
login shell：(/etc/profile -> [~/.bash\_profile | ~/.bash\_login | ~/.profile]  -> ~/.bashrc) 
取得 bash 时需要完整的登陆流程的，就称为 login shell。举例来说，你要由 tty1 ~ tty6 登陆，
需要输入用户的账号与密码，此时取得的 bash 就称为『 login shell 』啰；

non-login shell：(~/.bashrc) 
取得 bash 接口的方法不需要重复登陆的举动，举例来说，
(1)你以 X window 登陆 Linux 后， 再以 X 的图形化接口启动终端机，此时那个终端接口并没有需要再次的输入账号与密码，
    那个 bash 的环境就称为 non-login shell了。
(2)你在原本的 bash 环境下再次下达 bash 这个命令，同样的也没有输入账号密码， 那第二个 bash (子程序) 也是 non-login shell 。

logout shell: (~/.bash_logout) 
这个文件记录了『当我注销 bash 后，系统再帮我做完什么动作后才离开』
```

![](/images/login_shell_flow.png)

图 login shell 的配置文件读取流程

需要注意的是, /etc/bashrc 是 RedHat 系统，其他不同的 distributions 可能会放置在不同的档名就是了。由于这个 ~/.bashrc 会呼叫 /etc/bashrc 及 /etc/profile.d/*.sh ， 所以，万一你没有 ~/.bashrc (可能自己不小心将他删除了)，那么你会发现你的 bash 提示字符可能会变成这个样子： `-bash-3.2$  `

不要太担心啦！这是正常的，因为你并没有呼叫 /etc/bashrc 来规范 PS1 变量啦！而且这样的情况也不会影响你的 bash 使用。 如果你想要将命令提示字符捉回来，那么可以复制 /etc/skel/.bashrc 到你的家目录，再修订一下你所想要的内容， 并使用 source 去呼叫 ~/.bashrc ，那你的命令提示字符就会回来啦！

```bash
范例：列出所有的按键与按键内容
[root@www ~]# stty -a
speed 38400 baud; rows 24; columns 80; line = 0;
intr = ^C; quit = ^\; erase = ^?; kill = ^U; eof = ^D; eol = <undef>; 
eol2 = <undef>; swtch = <undef>; start = ^Q; stop = ^S; susp = ^Z; ...
[root@www ~]# stty erase ^h
```

```
[root@www ~]# help set
set: set [--abefhkmnptuvxBCHP] [-o option-name] [arg ...]
## Set or unset values of shell options and positional parameters.
## Change the value of shell attributes and positional parameters, ordisplay the names and values of shell variables.
Options:
  -a  Mark variables which are modified or created for export.
  -b  Notify of job termination immediately.
  -e  Exit immediately if a command exits with a non-zero status.
  -f  Disable file name generation (globbing).
  -h  Remember the location of commands as they are looked up.
  -k  All assignment arguments are placed in the environment for a command, not just those that precede the command name.
  -m  Job control is enabled.
  -n  Read commands but do not execute them.
  -o option-name
  -p  Turned on whenever the real and effective user ids do not match.
      Disables processing of the $ENV file and importing of shell
      functions.  Turning this option off causes the effective uid and
      gid to be set to the real uid and gid.
  -t  Exit after reading and executing one command.
  -u  Treat unset variables as an error when substituting.
  
[root@www ~]# echo $-
himBH
# 那个 $- 变量内容就是 set 的所有配置啦！ bash 默认是 himBH 喔！
```

其实我们还有其他的按键配置功能呢！就是在前一小节提到的 /etc/inputrc 这个文件里面配置  

```bash
[root@www ~]# cat /etc/inputrc
# do not bell on tab-completion
#set bell-style none

set meta-flag on
set input-meta on
set convert-meta off
set output-meta on
...
```

##<a name=13>[第十三章、学习 Shell Scripts](http://vbird.dic.ksu.edu.tw/linux_basic/0340bashshell-scripts.php)</a>

### 什么是 Shell Script

**shell script 的撰写中需要用到底下的注意事项:**

1. 如果读取到一个 Enter 符号 (CR) ，就尝试开始运行该行 (或该串) 命令；
2. 至於如果一行的内容太多，则可以使用『 \[Enter] 』来延伸至下一行；
3. 『 # 』可做为注解！任何加在 # 后面的数据将全部被视为注解文字而被忽略！
4. 第一行 #!/bin/bash 在宣告这个 script 使用的 shell 名称, 其他的 # 都是『注解』用途！
5. 使用 exit n ，这代表离开 script 并且回传一个 n 状态值给系统

要养成良好的 script 撰写习惯，在每个 script 的档头处记录好：

- script 的功能；
- script 的版本资讯；
- script 的作者与联络方式；
- script 的版权宣告方式；
- script 的 History (历史纪录)；
- script 内较特殊的命令，使用『绝对路径』的方式来下达；
- script 运行时需要的环境变量预先宣告与配置。

### 善用判断式
####利用 test 命令的测试功能:

1. 关於某个档名的『文件类型』判断，如 test -e filename 表示存在否

		-e	该『档名』是否存在？(常用)
		-f	该『档名』是否存在且为文件(file)？(常用)
		-d	该『档名』是否存在且为目录(directory)？(常用)
		-b	该『档名』是否存在且为一个 block device 装置？
		-c	该『档名』是否存在且为一个 character device 装置？
		-S	该『档名』是否存在且为一个 Socket 文件？
		-p	该『档名』是否存在且为一个 FIFO (pipe) 文件？
		-L	该『档名』是否存在且为一个连结档？
2. 关於文件的权限侦测，如 test -r filename 表示可读否 (但 root 权限常有例外)

		-r	侦测该档名是否存在且具有『可读』的权限？
		-w	侦测该档名是否存在且具有『可写』的权限？
		-x	侦测该档名是否存在且具有『可运行』的权限？
		-u	侦测该档名是否存在且具有『SUID』的属性？
		-g	侦测该档名是否存在且具有『SGID』的属性？
		-k	侦测该档名是否存在且具有『Sticky bit』的属性？
		-s	侦测该档名是否存在且为『非空白文件』？

3. 两个文件之间的比较，如： test file1 -nt file2

		-nt	(newer than)判断 file1 是否比 file2 新
		-ot	(older than)判断 file1 是否比 file2 旧
		-ef	判断 file1 与 file2 是否为同一文件，可用在判断 hard link 的判定上。 
            主要意义在判定，两个文件是否均指向同一个 inode 哩！
4. 关於两个整数之间的判定，例如 test n1 -eq n2

		-eq	两数值相等 (equal)
		-ne	两数值不等 (not equal)
		-gt	n1 大於 n2 (greater than)
		-lt	n1 小於 n2 (less than)
		-ge	n1 大於等於 n2 (greater than or equal)
		-le	n1 小於等於 n2 (less than or equal)
5. 判定字串的数据

		test -z string	判定字串是否为 0 ？若 string 为空字串，则为 true
		test -n string	判定字串是否非为 0 ？若 string 为空字串，则为 false。 注：-n 亦可省略
		test str1 = str2	判定 str1 是否等於 str2 ，若相等，则回传 true
		test str1 != str2	判定 str1 是否不等於 str2 ，若相等，则回传 false
6. 多重条件判定，例如： test -r filename -a -x filename
	
		-a	(and)两状况同时成立！例如 test -r file -a -x file，则 file 同时具有 r 与 x 权限时，才回传 true。
		-o	(or)两状况任何一个成立！例如 test -r file -o -x file，则 file 具有 r 或 x 权限时，就可回传 true。
		!	反相状态，如 test ! -x file ，当 file 不具有 x 时，回传 true

####利用判断符号 [ ]
- 在中括号 [] 内的每个组件都需要有空白键来分隔；
- 在中括号内的变量，最好都以双引号括号起来；
- 在中括号内的常数，最好都以单或双引号括号起来

####Shell script 的默认变量($0, $1...)： shift

```bash
[root@www ~]# name="VBird Tsai"
[root@www ~]# [ $name == "VBird" ] # equal to: [ VBird Tsai == "VBird" ]
bash: [: too many arguments
[root@www ~]# [ "$name" == "VBird" ] # equal to: [ "VBird Tsai" == "VBird" ]
```

```bash
/path/to/scriptname  opt1  opt2  opt3  opt4   
       $0             $1    $2    $3    $4
$? ：前一命令或者function回传值(exit value or return value), 正确会在 Linux 底下会回传一个 $? = 0 的值
$# ：代表后接的参数『个数』，以上表为例这里显示为『 4 』；
$@ ：代表『 "$1" "$2" "$3" "$4" 』之意，每个变量是独立的(用双引号括起来)；
$* ：代表『 "$1c$2c$3c$4" 』，其中 c 为分隔字节，默认为空白键， 所以本例中代表『 "$1 $2 $3 $4" 』之意。
```

**shift**：造成参数变量号码偏移($0 不变)

### 条件判断式
利用 if .... then： 

```bash
if [ 条件判断式 ]; then ## 而括号与括号之间，&& 代表 AND ；|| 代表 or ；
	当条件判断式成立时，可以进行的命令工作内容；
fi  ## 将 if 反过来写
#-------------------------------------------------
if [ 条件判断式 ]; then
	当条件判断式成立时，可以进行的命令工作内容；
else
	当条件判断式不成立时，可以进行的命令工作内容；
fi
#-------------------------------------------------
if [ 条件判断式一 ]; then
	当条件判断式一成立时，可以进行的命令工作内容；
elif [ 条件判断式二 ]; then
	当条件判断式二成立时，可以进行的命令工作内容；
else
	当条件判断式一与二均不成立时，可以进行的命令工作内容；
fi
```

利用 case ..... esac 判断

```bash
# read -p "Input your choice: " choice 
# case $choice in                      

case  $变量名称 in   <==关键字为 case ，还有变量前有钱字号
  "第一个变量内容")   <==每个变量内容建议用双引号括起来，关键字则为小括号 )
	程序段
	;;            <==每个类别结尾使用两个连续的分号来处理！
  "第二个变量内容")
	程序段
	;;
  *)                  <==最后一个变量内容都会用 * 来代表所有其他值
	不包含第一个变量内容与第二个变量内容的其他程序运行段
	exit 1
	;;
esac   
```

利用 function 功能

```bash
function fname() {
	程序段
}

function printit(){
	echo -n "My name is "     # 加上 -n 可以不断行继续在同一行显示
}

printit; echo "Leo"
```

### 回圈 (loop)
while...do...done, until...do...done (不定回圈)

```bash
while [ condition ]  <==中括号内的状态就是判断式
do                   <==do 是回圈的开始！
	程序段落
done                 <==done 是回圈的结束

until [ condition ]
do
	程序段落
done
```

for...do...done (固定回圈)

```bash
#users=$(cut -d ':' -f1 /etc/passwd)  # 撷取帐号名称
#for username in $users               # 开始回圈进行！

for var in con1 con2 con3 ...
do
	程序段
done
```
for...do...done 的数值处理

```bash
for (( 初始值; 限制值; 运行步阶 ))
do
	程序段
done

s=0; nu=100
for (( i=1; i<=$nu; i=i+1 ))
do
	s=$(($s+$i))
done
echo "The result of '1+2+3+...+100' is ==> $s"
```

在结束本shell之前，再跟大家补充两个与 loop 有关的命令：

```
* break 
* continue 
```
这两个命令常用在复合式循环里，也就是在do ... done之间又有更进一层的 loop,当然，用在单一循环中也未尝不可啦... ^_^ 
break 是用来打断循环，也就是"强迫结束" 循环。若 break 后面指定一个数值 n 的话，则"从里向外"打断第 n 个循环，默认值为 break 1 ，也就是打断当前的循环。 

在使用 break 时需要注意的是， 它与 return 及 exit 是不同的： 

```
* break 是结束 loop 
* return 是结束 function 
* exit 是结束 script/shell 
```
而 continue 则与 break 相反：强迫进入下一次循环动作。若你理解不来的话，那你可简单的看成：在 continue 到 done 之间的句子略过而返回循环顶端...与 break 相同的是：continue 后面也可指定一个数值 n ，以决定继续哪一层(从里向外计算)的循环，默认值为 continue 1 ，也就是继续当前的循环。 

### shell script 的追踪与 debug

```bash
[root@www ~]# sh [-nvx] scripts.sh
选项与参数：
-n  ：不要运行 script，仅查询语法的问题；
-v  ：再运行 sccript 前，先将 scripts 的内容输出到萤幕上；
-x  ：将使用到的 script 内容显示到萤幕上，这是很有用的参数！

范例：测试 sh16.sh 有无语法的问题？
[root@www ~]# sh -n sh16.sh 
# 若语法没有问题，则不会显示任何资讯！

范例：将 sh15.sh 的运行过程全部列出来～
[root@www ~]# sh -x sh15.sh  # 下面加号'+'真实中不会显示，这是只是增加效果
+ PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:/root/bin
+ export PATH
+ for animal in dog cat elephant
+ echo 'There are dogs.... '
There are dogs....
```
