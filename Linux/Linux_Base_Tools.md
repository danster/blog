# Linux Base Tools
<!-- toc -->
#### hot key
`ctrl` + `c` : cancel current cmd and stay at new line  
`ctrl` + `u` : clear current cmd   
`alt` + `shift` + `#` : make current cmd a comment if you want to keep it history  
`ctrl` + `l` : clear terminal and stay at current cmd, we can scroll back to view the last used commands  
`clear` : clear terminal and stay at new line, we can scroll back to view the last used commands   

#### ls
```
>ls -l  # shows file or dir, size, time, name, owner, permission ...
>ls -a  # views hidden files
>ls -lh # shows sizes in human readable format
>ls -F  # adds the ‘/’ Character at the end each directory
>ls -r  # displays files and directories in reverse order
>ls -R  # lists very long listing directory trees
>ls -lt # shows latest modification file or directory date as last
>ls -lS # displays file size in order
>ls -i  # displays Inode number of File or Directory
>ls -n  # displays UID and GID of files and directories
>ls -ld /tmp/  # displays information of /tmp directory

>alias ls="ls -l"
>unalias ls
```  
>Reference: http://www.tecmint.com/15-basic-ls-command-examples-in-linux/

#### bash wildcard
|符号|意义|
|---|---|
| * |代表『 0 个到无穷多个』任意字符|
| ? |代表『一定有一个』任意字符|
|[ ]|同样代表『一定有一个在括号内』的字符(非任意字符)。例如 [abcd] 代表『一定有一个字符， 可能是 a, b, c, d 这四个任何一个』|
| [ - ] |若有减号在中括号内时，代表『在编码顺序内的所有字符』。例如 [0-9] 代表 0 到 9 之间的所有数字，因为数字的语系编码是连续的！|
| [ ^ ] |若中括号内的第一个字符为指数符号 ( \^ ) ，那表示『反向选择』，例如 [\^abc] 代表 一定有一个字符，只要是非 a, b, c 的其他字符就接受的意思。|

#### bash 环境中的特殊符号

|符号|意义|
|---|---|
|#	|批注符号：这个最常被使用在 script 当中，视为说明！在后的数据均不运行
|\	|跳脱符号：将『特殊字符或通配符』还原成一般字符
| l	|管线 (pipe)：分隔两个管线命令的界定(后两节介绍)；
|;	|连续命令下达分隔符：连续性命令的界定 (注意！与管线命令并不相同)
|~	|用户的家目录
|$	|取用变量前导符：亦即是变量之前需要加的变量取代值
|&	|工作控制 (job control)：将命令变成背景下工作
|!	|逻辑运算意义上的『非』 not 的意思！
|/	|目录符号：路径分隔的符号
|>, >>	|数据流重导向：输出导向，分别是『取代』与『累加』
|<, <<	|数据流重导向：输入导向 (这两个留待下节介绍)
|' '	|单引号，不具有变量置换的功能
|" "	|具有变量置换的功能！
|\` \`	|两个『 ` 』中间为可以先运行的命令，亦可使用 $( )
|( )	|在中间为子 shell 的起始与结束
|{ }	|在中间为命令区块的组合！
|$? |前一命令回传值, 正确会在 Linux 底下会回传一个 $? = 0 的值  
|cmd ; cmd|不考虑命令相关性的连续命令下达,如：sync; sync; shutdown -h now
|cmd1 && cmd2	|`1. 若 cmd1 运行完毕且正确运行($?=0)，则开始运行 cmd2。`<br/>`2. 若 cmd1 运行完毕且为错误 ($?≠0)，则 cmd2 不运行。`|
|cmd1 ll cmd2|`1. 若 cmd1 运行完毕且正确运行($?=0)，则 cmd2 不运行。`<br/>`2. 若 cmd1 运行完毕且为错误 ($?≠0)，则开始运行 cmd2。`|

```
范例三：我不清楚 /tmp/abc 是否存在，但就是要创建 /tmp/abc/hehe 文件
[root@www ~]# ls /tmp/abc || mkdir /tmp/abc && touch /tmp/abc/hehe

情况一：(1)若 /tmp/abc 不存在故回传 $?≠0，则 (2)因为 || 遇到非为 0 的 $? 故开始 mkdir /tmp/abc，
由于 mkdir /tmp/abc 会成功进行，所以回传 $?=0 (3)因为 && 遇到 $?=0 故会运行 touch /tmp/abc/hehe，最终 hehe 就被创建了；  
情况二：(1)若 /tmp/abc 存在故回传 $?=0，则 (2)因为 || 遇到 0 的 $? 不会进行，此时 $?=0 继续向后传，
故 (3)因为 && 遇到 $?=0 就开始创建 /tmp/abc/hehe 了！最终 /tmp/abc/hehe 被创建起来。
```

- 标准输入　　(stdin) ：代码为 0 ，使用 < 或 << ；
- 标准输出　　(stdout)：代码为 1 ，使用 > 或 >> ；
- 标准错误输出(stderr)：代码为 2 ，使用 2> 或 2>> ；
- 垃圾桶黑洞装置与特殊写法: /dev/null 
- &0代表stdin, &1代表stdout, &2代表stderr
- command > out.file  2> &1 标准输出重定向到out.file文件，即将标准出错也输出到out.file文件中

#### 撷取命令：cut,grep

> 撷取命令就是将一段数据经过分析后，取出我们所想要的。或者是经由分析关键词，取得我们所想要的那一行！   
> 一般来说，撷取信息通常是针对『一行一行』来分析的， 并不是整篇信息分析的.

cut 是将一行信息当中，取出某部分我们想要的，  
grep 则是分析一行信息， 若当中有我们所需要的信息，就将该行拿出来.

```
[root@www ~]# cut -d'分隔字符' -f fields <==用于有特定分隔字符
[root@www ~]# cut -c 字符区间            <==用于排列整齐的信息
选项与参数：
-d  ：后面接分隔字符。与 -f 一起使用；
-f  ：依据 -d 的分隔字符将一段信息分割成为数段，用 -f 取出第几段的意思；
-c  ：以字符 (characters) 的单位取出固定字符区间；

[root@www ~]# echo $PATH
/bin:/usr/bin:/sbin:/usr/sbin:/usr/local/bin:/usr/X11R6/bin:/usr/games:
# 1 |    2   |  3  |    4    |       5      |     6        |    7

[root@www ~]# echo $PATH | cut -d ':' -f 5   #/usr/local/bin 
[root@www ~]# echo $PATH | cut -d ':' -f 3,5 #/sbin:/usr/local/bin
[root@www ~]# export | cut -c 2-5 #bin:
```

```
[root@www ~]# grep [-acinv] [--color=auto] '搜寻字符串' filename
选项与参数：
-a ：将 binary 文件以 text 文件的方式搜寻数据
-c ：计算找到 '搜寻字符串' 的次数
-i ：忽略大小写的不同，所以大小写视为相同
-n ：顺便输出行号
-v ：反向选择，亦即显示出没有 '搜寻字符串' 内容的那一行！
--color=auto ：可以将找到的关键词部分加上颜色的显示喔！

```

#### 排序命令：sort, wc, uniq

```
[root@www ~]# sort [-fbMnrtuk] [file or stdin]
选项与参数：
-f  ：忽略大小写的差异，例如 A 与 a 视为编码相同；
-b  ：忽略最前面的空格符部分；
-M  ：以月份的名字来排序，例如 JAN, DEC 等等的排序方法；
-n  ：使用『纯数字』进行排序(默认是以文字型态来排序的)；
-r  ：反向排序；
-u  ：就是 uniq ，相同的数据中，仅出现一行代表；
-t  ：分隔符，默认是用 [tab] 键来分隔；
-k  ：以那个区间 (field) 来进行排序的意思

范例：/etc/passwd 内容是以 : 来分隔的，我想以第三栏来排序，该如何？
[root@www ~]# cat /etc/passwd | sort -t ':' -k 3
root:x:0:0:root:/root:/bin/bash
uucp:x:10:14:uucp:/var/spool/uucp:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
bin:x:1:1:bin:/bin:/sbin/nologin
```

```
[root@www ~]# uniq [-ic]
选项与参数：
-i  ：忽略大小写字符的不同；
-c  ：进行计数

范例一：使用 last 将账号列出，仅取出账号栏，进行排序后仅取出一位；
[root@www ~]# last | cut -d ' ' -f1 | sort | uniq

范例二：承上题，如果我还想要知道每个人的登陆总次数呢？
[root@www ~]# last | cut -d ' ' -f1 | sort | uniq -c
     12 reboot
     41 root
```

```
[root@www ~]# wc [-lwm]
选项与参数：
-l  ：仅列出行；
-w  ：仅列出多少字(英文单字)；
-m  ：多少字符；

范例：那个 /etc/man.config 里面到底有多少相关字、行、字符数？
[root@www ~]# cat /etc/man.config | wc 
    141     722    4617
#   行      字数    字符数
```
####<a name=tee>双向重导向 tee：同时将数据流分送到文件去与stdout</a>

```
[root@www ~]# tee [-a] file
-a  ：以累加 (append) 的方式，将数据加入 file 当中！

[root@www ~]# ls -l / | tee -a ~/homefile | more
# 要注意！ tee 后接的文件会被覆盖，若加上 -a 这个选项则能将信息累加。
```
#### 字符转换命令：tr, col, join, paste, expand

- tr: 可以用来删除一段信息当中的文字，或者是进行文字信息的替换
- col: 将 [tab] 按键取代成为空格键
- join: 处理『两个文件当中，有 "相同数据" 的那一行，才将他加在一起』
- paste: 将两行贴在一起，且中间以 [tab] 键隔开
- expand: 将 [tab] 按键转成空格键

```
[root@www ~]# tr word1 word2 stdin # 所有word1用word2替代
[root@www ~]# tr [-ds] SET1 ...
-d  ：删除信息当中的 SET1 这个字符串；
-s  ：取代掉重复的字符！

# tr 是由正规表示法的方式来取代数据
[root@www ~]# last | tr '[a-z]' '[A-Z]' or tr [a-z] [A-Z]  #小写变成大写
[root@www ~]# cat /etc/passwd | tr -d ':' # 将冒号 (:) 删除
```

```
[root@www ~]# col [-xb]
-x  ：将 tab 键转换成对等的空格键
-b  ：在文字内有反斜杠 (/) 时，仅保留反斜杠最后接的那个字符
```

```
[root@www ~]# paste [-ds] file1 file2
-d  ：后面可以接分隔字符。默认是以 [tab] 来分隔的！
-s  ：paste one file at a time instead of in parallel. --serial
-   ：如果 file 部分写成 - ，表示来自 stdin。
```

```
[root@www ~]# expand [-t] file
-t  ：自行定义一个 [tab] 按键代表多少个字符,默认 8 个。
```

```
[root@www ~]# join [-ti12] file1 file2
-t  ：join 默认以空格符分隔数据，并且比对『第一个字段』的数据，
      如果两个文件相同，则将两笔数据联成一行，且第一个字段放在第一个！
-i  ：忽略大小写的差异；
-1  ：这个是数字的 1 ，代表『第一个文件要用那个字段来分析』的意思；
-2  ：代表『第二个文件要用那个字段来分析』的意思。

# 需要特别注意的是，在使用 join 之前，你所需要处理的文件应该要
# 事先经过排序 (sort) 处理！ 否则有些比对的项目会被略过!

范例：/etc/passwd 第四字段和/etc/group 的第三字段都是GID，请将两个文件整合？
[root@www ~]# head -n 3 /etc/passwd /etc/group
==> /etc/passwd <==
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin

==> /etc/group <==
root:x:0:root
bin:x:1:root,bin,daemon

[root@www ~]# join -t ':' -1 4 /etc/passwd -2 3 /etc/group
0:root:x:0:root:/root:/bin/bash:root:x:root
1:bin:x:1:bin:/bin:/sbin/nologin:bin:x:root,bin,daemon
# 同样的，相同的字段部分被移动到最前面了！所以第二个文件的内容就没再显示。
```

#### 分割命令 split: 依据文件大小或行数来分割成为小文件

```
[root@www ~]# split [-bl] file PREFIX
-b  ：后面可接欲分割成的文件大小，可加单位，例如 b, k, m 等；
-l  ：以行数来进行分割。
PREFIX ：代表前导符的意思，可作为分割文件的前导文字。

范例：我的 /etc/termcap 有七百多K，若想要分成 300K 一个文件时？
[root@www ~]# split -b 300k /etc/termcap termcap
[root@www ~]# ll termcap*
-rw-r--r-- 1 root root 300 Feb  7 16:39 termcapaa
-rw-r--r-- 1 root root 300 Feb  7 16:39 termcapab
-rw-r--r-- 1 root root 189 Feb  7 16:39 termcapac
# 小文件会以xxxaa, xxxab, xxxac 等方式来创建！
```

#### 参数代换 xargs：产生某个命令的参数

xargs 可以读入 stdin 的数据，并且以空格符或断行字符作为分辨，将 stdin 的数据分隔成为 arguments 。

```
[root@www ~]# xargs [-0epn] command
选项与参数：
-0  ：如果输入的 stdin 含有特殊字符，例如 `, \, 空格键等等字符时，这个 -0 参数
      可以将他还原成一般字符。这个参数可以用于特殊状态喔！
-e  ：这个是 EOF (end of file) 的意思。后面可以接一个字符串，当 xargs 分析到
      这个字符串时，就会停止继续工作！
-p  ：在运行每个命令的 argument 时，都会询问使用者的意思；
-n  ：后面接次数，每次 command 命令运行时，要使用几个参数的意思。看范例,
当 xargs 后面没有接任何的命令时，默认是以 echo 来进行输出喔！

范例：将所有的 /etc/passwd 内的账号都以 finger 查阅，但一次仅查阅五个账号
[root@www ~]# cut -d':' -f1 /etc/passwd | xargs -p -n 5 finger
finger root bin daemon adm lp ?...y
.....(中间省略)....
finger uucp operator games gopher ftp ?...y
.....(底下省略)....

范例：找出 /sbin 底下具有特殊权限的档名，并使用 ls -l 列出详细属性
[root@www ~]# find /sbin -perm +7000 | ls -l
# 结果竟然仅有列出 root 所在目录下的文件！这不是我们要的！
# 因为 ll (ls) 并不是管线命令的原因啊！

[root@www ~]# find /sbin -perm +7000 | xargs ls -l
-rwsr-xr-x 1 root root 70420 May 25  2008 /sbin/mount.nfs
-rwsr-xr-x 1 root root 70424 May 25  2008 /sbin/mount.nfs4
-rwxr-sr-x 1 root root  5920 Jun 15  2008 /sbin/netreport
....(底下省略)....
```

#### 文件比对工具 diff, cmp, pacth
```
# diff --help
Usage: diff [OPTION]... FILES # FILES are `FILE1 FILE2' or `DIR1 DIR2' or `DIR FILE...' or `FILE... DIR'.
Compare files line by line.
If --from-file or --to-file is given, there are no restrictions on FILES. If a FILE is `-', read standard input.
Exit status is 0 if inputs are the same, 1 if different, 2 if trouble.

-r  --recursive  Recursively compare any subdirectories found.
-N  --new-file  Treat absent files as empty.
-i  --ignore-case  Ignore case differences in file contents.
--ignore-file-name-case  Ignore case when comparing file names.
--no-ignore-file-name-case  Consider case when comparing file names.
-E  --ignore-tab-expansion  Ignore changes due to tab expansion.
-b  --ignore-space-change  Ignore changes in the amount of white space.
-w  --ignore-all-space  Ignore all white space.
-B  --ignore-blank-lines  Ignore changes whose lines are all blank.
-I RE  --ignore-matching-lines=RE  Ignore changes whose lines all match RE.
--strip-trailing-cr  Strip trailing carriage return on input.
-a  --text  Treat all files as text.

--normal  Output a normal diff (default output).
-q  --brief  Output only whether files differ.
-c  -C NUM  --context[=NUM]  Output NUM (default 3) lines of copied context.
-u  -U NUM  --unified[=NUM]  Output NUM (default 3) lines of unified context.
-e  --ed  Output an ed script.
-n  --rcs  Output an RCS format diff.
-y  --side-by-side  Output in two columns.

...
```

```
# patch --help
Usage: patch [OPTION]... [ORIGFILE [PATCHFILE]]

Input options:
  -p NUM  --strip=NUM  Strip NUM leading components from file names.
  -F LINES  --fuzz LINES  Set the fuzz factor to LINES for inexact matching.
  -l  --ignore-whitespace  Ignore white space changes between patch and input.
  -c  --context  Interpret the patch as a context difference.
  -e  --ed  Interpret the patch as an ed script.
  -n  --normal  Interpret the patch as a normal difference.
  -u  --unified  Interpret the patch as a unified difference.
  -N  --forward  Ignore patches that appear to be reversed or already applied.
  -R  --reverse  Assume patches were created with old and new files swapped.
  -i PATCHFILE  --input=PATCHFILE  Read patch from PATCHFILE instead of stdin.

Output options:
  -o FILE  --output=FILE  Output patched files to FILE.
  -r FILE  --reject-file=FILE  Output rejects to FILE.
  -D NAME  --ifdef=NAME  Make merged if-then-else output using NAME.
  -E  --remove-empty-files  Remove output files that are empty after patching.
  -Z  --set-utc  Set times of patched files, assuming diff uses UTC (GMT).
  -T  --set-time  Likewise, assuming local time.

...
```

```
# cmp --help
Usage: cmp [OPTION]... FILE1 [FILE2 [SKIP1 [SKIP2]]]
Compare two files byte by byte.

  -b  --print-bytes  Print differing bytes.
  -i SKIP  --ignore-initial=SKIP  Skip the first SKIP bytes of input.
  -i SKIP1:SKIP2  --ignore-initial=SKIP1:SKIP2\
  -l  --verbose  Output byte numbers and values of all differing bytes.
  -n LIMIT  --bytes=LIMIT  Compare at most LIMIT bytes.
  -s  --quiet  --silent  Output nothing; yield exit status only.

SKIP values may be followed by the following multiplicative suffixes:
kB 1000, K 1024, MB 1,000,000, M 1,048,576, 
GB 1,000,000,000, G 1,073,741,824, and so on for T, P, E, Z, Y.

If a FILE is `-' or missing, read standard input.
Exit status is 0 if inputs are the same, 1 if different, 2 if trouble.
```

Usage Example:

```
# ll tomcat.before/
-rw-r--r-- 1 root root 3639 Nov 27 02:37 Makefile
-rw-r--r-- 1 root root 2257 Nov 27 03:59 tomcat.init
drwxr-xr-x 3 root root 4096 Nov 27 02:43 rpmbuild
drwxr-xr-x 3 root root 4096 Nov 27 03:59 rpmbuild/BUILD

# ll tomcat.after/
-rw-r--r-- 1 root root 3286 Nov 27 02:38 tomcat.init
-rw-r--r-- 1 root root 2257 Nov 27 02:38 tomcat.init.bak
drwxr-xr-x 6 root root 4096 Nov 27 02:38 rpmbuild
drwxr-xr-x 4 root root 4096 Nov 27 02:38 rpmbuild/BUILD
-rwxr-xr-x 3 root root 4096 Nov 27 02:38 rpmbuild/apache-tomcat.rpm # a binary file

# diff -qr tomcat.before tomcat.after
Only in tomcat.before: Makefile
Files tomcat.before/rpmbuild/BUILD/etc/init.d/tomcat and tomcat.after/rpmbuild/BUILD/etc/init.d/tomcat differ
Only in tomcat.after/rpmbuild/BUILD: opt
Only in tomcat.after/rpmbuild: apache-tomcat.rpm
Files tomcat.before/tomcat.init and tomcat.after/tomcat.init differ
Only in tomcat.after: tomcat.init.bak
```
operation 1

```
# diff -ruN tomcat.before tomcat.after > tomcat.after.patch
# cd tomcat.before
# patch -p1 -E < ../tomcat.after.patch
patching file Makefile
patching file rpmbuild/BUILD/etc/init.d/tomcat
...
patching file rpmbuild/BUILD/opt/cloudcommon/apache-tomcat/webapps/manager/xform.xsl
patching file tomcat.init
patching file tomcat.init.bak
# ll
-rw-r--r-- 1 root root 3286 Nov 27 07:15 tomcat.init
-rw-r--r-- 1 root root 2257 Nov 27 07:15 tomcat.init.bak
drwxr-xr-x 3 root root 4096 Nov 27 02:43 rpmbuild
drwxr-xr-x 3 root root 4096 Nov 27 02:43 rpmbuild/BUILD

# patch -p1 -ER < ../tomcat.after.patch ; ll
-rw-r--r-- 1 root root 3639 Nov 27 07:17 Makefile
drwxr-xr-x 3 root root 4096 Nov 27 02:43 rpmbuild
-rw-r--r-- 1 root root 2257 Nov 27 07:17 tomcat.init
drwxr-xr-x 3 root root 4096 Nov 27 02:43 rpmbuild/BUILD
```

```
# diff -ruNa tomcat.before tomcat.after > tomcat.after.patch
# cd tomcat.before
# patch -p1 -E < ../tomcat.after.patch; ll
-rw-r--r-- 1 root root 3286 Nov 27 07:15 tomcat.init
-rw-r--r-- 1 root root 2257 Nov 27 07:15 tomcat.init.bak
drwxr-xr-x 3 root root 4096 Nov 27 02:43 rpmbuild
drwxr-xr-x 3 root root 4096 Nov 27 02:43 rpmbuild/BUILD
-rwxr-xr-x 3 root root 4096 Nov 27 02:38 rpmbuild/apache-tomcat.rpm # a binary file
# patch -p1 -ER < ../tomcat.after.patch
```

operation 2

```
# diff -ru tomcat.before tomcat.after > tomcat.after.patch
# cd tomcat.before/
# patch [-E] -p1 < ../tomcat.after.patch
patching file rpmbuild/BUILD/etc/init.d/tomcat
patching file tomcat.init
# ll
-rw-r--r-- 1 root root 3639 Nov 27 07:17 Makefile
-rw-r--r-- 1 root root 3286 Nov 27 07:20 tomcat.init
drwxr-xr-x 3 root root 4096 Nov 27 02:43 rpmbuild
drwxr-xr-x 3 root root 4096 Nov 27 07:17 rpmbuild/BUILD
# patch -p1 -[E] -R < ../tomcat.after.patch
```
operation 3

```
# diff -r tomcat.before tomcat.after > tomcat.after.patch
# cd tomcat.before/
# patch [-E] -p1 < ../tomcat.after.patch # failed
can't find file to patch at input line 3
Perhaps you used the wrong -p or --strip option?
```
