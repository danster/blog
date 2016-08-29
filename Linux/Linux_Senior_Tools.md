## Linux Senior Tools
#### Index
- [基础正规表示式字符汇整](#reg)
- [sed](#sed)
- [awk](#awk)
- [Linux工具快速教程](#tool)

###<a name=reg>基础正规表示式字符汇整</a>
| RE 字符|	意义与范例 |
|--------|----------|
|^word| 意义：待搜寻的字串(word)在行首！<br/>范例：搜寻行首为 # 开始的那一行，并列出行号<br/>grep -n '^#' regular_express.txt
|word$|意义：待搜寻的字串(word)在行尾！<br/>范例：将行尾为 ! 的那一行列印出来，并列出行号<br/>grep -n '!$' regular_express.txt
|.	|意义：代表『一定有一个任意字节』的字符！<br/>范例：搜寻的字串可以是 (eve) (eae) (eee) (e e)， 但不能仅有 (ee) ！亦即 e 与 e 中间『一定』仅有一个字节，而空白字节也是字节！<br/> grep -n 'e.e' regular_express.txt
| \   |意义：跳脱字符，将特殊符号的特殊意义去除！<br/>范例：搜寻含有单引号 ' 的那一行！<br/>grep -n \' regular_express.txt
| *   |意义：重复零个到无穷多个的前一个 RE 字符<br/>范例：找出含有 (es) (ess) (esss) 等等的字串，注意，因为 * 可以是 0 个，所以 es 也是符合带搜寻字串。另外，因为 * 为重复『前一个 RE 字符』的符号， 因此，在 * 之前必须要紧接著一个 RE 字符喔！例如任意字节则为 『.\*』 ！<br/>grep -n 'ess*' regular_express.txt
|[list]| 意义：字节集合的 RE 字符，里面列出想要撷取的字节！<br/>范例：搜寻含有 (gl) 或 (gd) 的那一行，需要特别留意的是，在 [] 当中『谨代表一个待搜寻的字节』， 例如『 a[afl]y 』代表搜寻的字串可以是 aay, afy, aly 即 [afl] 代表 a 或 f 或 l 的意思！<br/>grep -n 'g[ld]' regular_express.txt
|[n1-n2]|	意义：字节集合的 RE 字符，里面列出想要撷取的字节范围！<br/>范例：搜寻含有任意数字的那一行！需特别留意，在字节集合 [] 中的减号 - 是有特殊意义的，他代表两个字节之间的所有连续字节！但这个连续与否与 ASCII 编码有关，因此，你的编码需要配置正确(在 bash 当中，需要确定 LANG 与 LANGUAGE 的变量是否正确！) 例如所有大写字节则为 [A-Z]<br/>grep -n '[A-Z]' regular_express.txt
|[^list]|	意义：字节集合的 RE 字符，里面列出不要的字串或范围！<br/>范例：搜寻的字串可以是 (oog) (ood) 但不能是 (oot) ，那个 ^ 在 [] 内时，代表的意义是『反向选择』的意思。 例如，我不要大写字节，则为 [^A-Z]。<br/><br/>但是，需要特别注意的是，如果以 grep -n [^A-Z] regular\_express.txt 来搜寻，却发现该文件内的所有行都被列出，为什么？因为这个 [^A-Z] 是『非大写字节』的意思， 因为每一行均有非大写字节 <br/>例如第一行的 "Open Source" 就有 p,e,n,o.... 等等的小写字<br/>grep -n 'oo[^t]' regular_express.txt
|\{n,m\}|	意义：连续 n 到 m 个的『前一个 RE 字符』<br/>意义：若为 \{n\} 则是连续 n 个的前一个 RE 字符，<br/>意义：若是 \{n,\} 则是连续 n 个以上的前一个 RE 字符！<br/>范例：在 g 与 g 之间有 2 个到 3 个的 o 存在的字串，亦即 (goog)(gooog)<br/>grep -n 'go\{2,3\}g' regular_express.txt

- grep, sed, tr, vim支持：正规表示式
- ls 不支持正规表示式

### <a name=sed>sed</a>
管道命令 sed 可以将数据进行取代、删除、新增、撷取特定行等等的功能

```
[root@www ~]# sed [-nefr] [动作]
选项与参数：
-n  ：使用安静(silent)模式。在一般 sed 的用法中，所有来自 STDIN 
      的数据一般都会被列出到萤幕上。但如果加上 -n 参数后，则只有经过
      sed 特殊处理的那一行(或者动作)才会被列出来。
-e  ：直接在命令列模式上进行 sed 的动作编辑；
-f  ：直接将 sed 的动作写在一个文件内， -f filename 则可以运行 filename 内的 
      sed 动作；
-r  ：sed 的动作支持的是延伸型正规表示法的语法。(默认是基础正规表示法语法)
-i  ：直接修改读取的文件内容，而不是由萤幕输出。

动作说明：[n1[,n2]]function
n1, n2：不见得会存在，一般代表『选择进行动作的行数』，举例来说，如果我的动作
         是需要在 10 到 20 行之间进行的，则『 10,20[动作行为] 』

function 有底下这些咚咚：
a   ：新增， a 的后面可以接字串，而这些字串会在新的一行出现(目前的下一行)～
c   ：取代， c 的后面可以接字串，这些字串可以取代 n1,n2 之间的行！
d   ：删除，因为是删除啊，所以 d 后面通常不接任何咚咚；
i   ：插入， i 的后面可以接字串，而这些字串会在新的一行出现(目前的上一行)；
p   ：列印，亦即将某个选择的数据印出。通常 p 会与参数 sed -n 一起运行～
s   ：取代，可以直接进行取代的工作哩！通常这个 s 的动作可以搭配
      正规表示法！例如 1,20s/old/new/g 就是啦！
```
> 以行为单位的新增/删除功能  

```
范例一：将 /etc/passwd 的内容列出并且列印行号，同时，请将第 2~5 行删除！
[root@www ~]# nl /etc/passwd | sed '2,5d'
[root@www ~]# nl /etc/passwd | sed '3,$d' #『 $ 』代表最后一行

范例三：在第二行后面加入两行字，例如『Drink tea or 』与『drink beer?』
[root@www ~]# nl /etc/passwd | sed '2a Drink tea or \
> drink beer ?'

范例四：我想将第2-5行的内容取代成为『No 2-5 number』呢？
[root@www ~]# nl /etc/passwd | sed '2,5c No 2-5 number'

范例五：仅列出 /etc/passwd 文件内的第 5-7 行
[root@www ~]# nl /etc/passwd | sed -n '5,7p'
# 如果不使用 -n(安静模式)，5-7 行会重复输出
```
> 部分数据的搜寻并取代的功能: `sed 's/要被取代的字串/新的字串/g'`

```
# 删除掉注解之后的数据！
[root@www ~]# cat file | sed 's/#.*$//g'

范例六：[危险] 利用 sed 将 regular_express.txt 内每一行结尾若为 . 则换成 !
[root@www ~]# sed -i 's/\.$/\!/g' regular_express.txt
# 上头的 -i 选项可以让你的 sed 直接去修改后面接的文件内容而不是由萤幕输出喔！
```
#### <a name=awk>awk</a>
**AWK语言详解**: http://awk.readthedocs.io/en/latest/chapter-two.html  

相较於 sed 常常作用於一整个行的处理，awk 则比较倾向於一行当中分成数个『栏位』来处理。因此，awk 相当的适合处理小型的数据数据处理呢！   
awk 通常运行的模式是这样的：
> awk '条件类型1{动作1} 条件类型2{动作2} ...' filename

- awk 是『以行为一次处理的单位』， 而『以栏位为最小的处理单位』
- 每一行的每个栏位都是有变量名称的，那就是 $1, $2... 等变量名称
- $0 for 一整列数据, NF for 每行的栏位总数, NR for 目前处理的是第几行, FS for 目前的分隔字节，默认是空白键

我的例子：  
```shell
# cat data
Beth    4.00    0
Dan     3.75    0
kathy   4.00    10
Mark    5.00    20
Mary    5.50    22
Susie   4.25    18

# cat awk.cmd
BEGIN {print "Name   Rate  Hours"}
{print $0}
$2 > 5 || $3 > 20 {
      # comments
      line[NR] = $0;
}
/Susie/ {print NR, "Susie"}
/Dan/,/Susie/ {print NR, "Dan-Susie"}

END {
  for(i=1; i<=NR; i+=1)
    print i, line[i]
}
# awk -f awk.cmd data
Name   Rate  Hours
Beth    4.00    0
Dan     3.75    0
2 Dan-Susie
kathy   4.00    10
3 Dan-Susie
Mark    5.00    20
4 Dan-Susie
Mary    5.50    22
5 Dan-Susie
Susie   4.25    18
6 Susie
6 Dan-Susie
1
2
3
4
5 Mary  5.50    22
6
```

VBird 例子：  
```
# 取出帐号与登陆者的 IP ，且帐号与 IP 之间以 [tab] 隔开
>last -n 5 | awk '{print $1 "\t" $3}'

>last -n 5| awk '{print $1 "\t lines: " NR "\t columns: " NF}'
root     lines: 1        columns: 10
root     lines: 2        columns: 10

>cat /etc/passwd | awk '{FS=":"} $3 < 10 {print $1 "\t " $3}'
root:x:0:0:root:/root:/bin/bash
bin      1
daemon   2
....(以下省略)....
```
### <a name=tool>Linux工具快速教程</a>

<div class="toctree-wrapper compound">
<ul>
<li class="toctree-l1"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/base/index.html">Linux基础</a><ul>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/base/01_use_man.html">1. 学会使用命令帮助</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/base/02_file_manage.html">2. 文件及目录管理</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/base/03_text_processing.html">3. 文本处理</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/base/04_disk.html">4. 磁盘管理</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/base/05_process_manage.html">5. 进程管理工具</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/base/06_monitor.html">6. 性能监控</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/base/07_network.html">7. 网络工具</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/base/08_user_manage.html">8. 用户管理工具</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/base/09_system_manage.html">9. 系统管理及IPC资源管理</a></li>
</ul>
</li>
<li class="toctree-l1"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/advance/index.html">Linux工具进阶</a><ul>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/advance/01_program_build.html">1. 程序构建</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/advance/02_program_debug.html">2. 程序调试</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/advance/03_optimization.html">3. 性能优化</a></li>
</ul>
</li>
<li class="toctree-l1"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/index.html">工具参考篇</a><ul>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/gdb.html">1. gdb 调试利器</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/ldd.html">2. ldd 查看程序依赖库</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/lsof.html">3. lsof 一切皆文件</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/ps.html">4. ps 进程查看器</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/pstack.html">5. pstack 跟踪进程栈</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/strace.html">6. strace 跟踪进程中的系统调用</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/ipcs.html">7. ipcs 查询进程间通信状态</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/top.html">8. top linux下的任务管理器</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/free.html">9. free 查询可用内存</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/vmstat.html">10. vmstat 监视内存使用情况</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/iostat.html">11. iostat 监视I/O子系统</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/sar.html">12. sar 找出系统瓶颈的利器</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/readelf.html">13. readelf elf文件格式分析</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/objdump.html">14. objdump 二进制文件分析</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/nm.html">15. nm 目标文件格式分析</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/size.html">16. size 查看程序内存映像大小</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/wget.html">17. wget 文件下载</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/scp.html">18. scp 跨机远程拷贝</a></li>
<li class="toctree-l2"><a class="reference internal" href="http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html">19. crontab 定时任务</a></li>
</ul>
</li>
</ul>
</div>
