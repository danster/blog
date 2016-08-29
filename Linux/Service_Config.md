## Linux Service Configuration
### Index
- [Common Configration](#com)
- [User Management](#user)
- [Network](#net)
- [Package Source](#source)
- [Locale](#locale)
- [Samba](#samba)
- [Tmux](#tmux)
- [ngrok,mobi](#ngrok)
- [Vi/Vim](#vim)

### Common Configration
- [ubuntu] locale   
```
# vi /etc/default/locale 
LC_ALL=en_US.UTF-8 
LANG=en_US.UTF-8 
LANGUAGE=en_US.UTF-8
# reboot
```
- [ubuntn] hostname => vi /etc/hostname

### User Management
- create a user: useradd
- create a group: groupadd
- change : chown, chmod
- add user to sudoer: sudo adduser <username> sudo  or vi /etc/sudoers
- find out user group identity: id -nG {user-name} 
- add a existing user to existing group : usermod -a {groupname1,groupname2,...} {username}
- remove a existing user from existing groups: usermod -G {groupname1,groupname2,...} {username}

### Network

**CentOS**: http://blog.earth-works.com/2012/09/16/setting-up-centos-configure-the-network/

```
[leo@centOS ~]$ cat /etc/sysconfig/network-scripts/ifcfg-eth0
DEVICE=eth0
BOOTPROTO=none
ONBOOT=yes
HWADDR=00:50:56:A7:67:E5
TYPE=Ethernet
UUID=9a6d3c91-621b-4573-a8f4-d4e62ec332ea
IPV6INIT=no
IPADDR=10.32.119.132
NETMASK=255.255.255.0
GATEWAY=10.32.119.1
BROADCAST=10.32.119.255

[leo@centOS ~]$ cat /etc/sysconfig/network
NETWORKING=yes
NETWORKING_IPV6=no
HOSTNAME=centOS
DOMAINNAME=lss.xxx.com

[leo@centOS ~]$ cat /etc/resolv.conf
nameserver 10.254.174.10
domain lss.xxx.com
```

**Ubuntu**： http://thesimplesynthesis.com/post/how-to-set-a-static-ip-and-dns-in-ubuntu-14-04
 
### Package Source
- yum: http://liaoph.com/linux-yum-tutorial/
- apt-get: vi /etc/apt/sources.list  
```
#deb cdrom:[Ubuntu-Server 14.04.1 LTS _Trusty Tahr_ - Release amd64 (20140722.3)]/ trusty main restricted

# See http://help.ubuntu.com/community/UpgradeNotes for how to upgrade to
# newer versions of the distribution.
deb http://us.archive.ubuntu.com/ubuntu/ trusty main restricted
deb-src http://us.archive.ubuntu.com/ubuntu/ trusty main restricted

## Major bug fix updates produced after the final release of the
## distribution.
deb http://us.archive.ubuntu.com/ubuntu/ trusty-updates main restricted
deb-src http://us.archive.ubuntu.com/ubuntu/ trusty-updates main restricted

## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu
## team. Also, please note that software in universe WILL NOT receive any
## review or updates from the Ubuntu security team.
deb http://us.archive.ubuntu.com/ubuntu/ trusty universe
deb-src http://us.archive.ubuntu.com/ubuntu/ trusty universe
deb http://us.archive.ubuntu.com/ubuntu/ trusty-updates universe
deb-src http://us.archive.ubuntu.com/ubuntu/ trusty-updates universe

## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu
## team, and may not be under a free licence. Please satisfy yourself as to
## your rights to use the software. Also, please note that software in
## multiverse WILL NOT receive any review or updates from the Ubuntu
## security team.
deb http://us.archive.ubuntu.com/ubuntu/ trusty multiverse
deb-src http://us.archive.ubuntu.com/ubuntu/ trusty multiverse
deb http://us.archive.ubuntu.com/ubuntu/ trusty-updates multiverse
deb-src http://us.archive.ubuntu.com/ubuntu/ trusty-updates multiverse

## N.B. software from this repository may not have been tested as
## extensively as that contained in the main release, although it includes
## newer versions of some applications which may provide useful features.
## Also, please note that software in backports WILL NOT receive any review
## or updates from the Ubuntu security t.
deb http://us.archive.ubuntu.com/ubuntu/ trusty-backports main restricted universe multiverse
deb-src http://us.archive.ubuntu.com/ubuntu/ trusty-backports main restricted universe multiverse

deb http://security.ubuntu.com/ubuntu trusty-security main restricted
deb-src http://security.ubuntu.com/ubuntu trusty-security main restricted
deb http://security.ubuntu.com/ubuntu trusty-security universe
deb-src http://security.ubuntu.com/ubuntu trusty-security universe
deb http://security.ubuntu.com/ubuntu trusty-security multiverse
deb-src http://security.ubuntu.com/ubuntu trusty-security multiverse

## Uncomment the following two lines to add software from Canonical's
## 'partner' repository.
## This software is not part of Ubuntu, but is offered by Canonical and the
## respective vendors as a service to Ubuntu users.
# deb http://archive.canonical.com/ubuntu trusty partner
# deb-src http://archive.canonical.com/ubuntu trusty partner

## Uncomment the following two lines to add software from Ubuntu's
## 'extras' repository.
## This software is not part of Ubuntu, but is offered by third-party
## developers who want to ship their latest software.
# deb http://extras.ubuntu.com/ubuntu trusty main
# deb-src http://extras.ubuntu.com/ubuntu trusty main
```

### Locale
所謂的語系(locale)，其實包含語言及地區的設定，因為除了語言之外，國家與國家的日期表示方式、數字格式、貨幣符號、度量單位可能都不一樣，所以才會有了這個locale的發明，依照國家和語言給予分類編號，再加上語言編碼，就構成了locale。

- 列出已安裝的語系: `$ locale -a`
- 列出目前的語系設定: `$ locale`

**ubuntu**: http://www.davidpai.tw/ubuntu/2011/ubuntu-set-locale/

Ubuntu所有支援的語系和編碼，可以看這個檔案: /usr/share/i18n/SUPPORTED  
產生語系檔案: `$ sudo locale-gen zh_TW zh_TW.UTF-8 zh_CN.UTF-8 en_US.UTF-8` ,檔案就放在 /usr/lib/locale 目錄下， 會產生與語系同名的目錄.

更改語系設定: `$ export LC_TIME=zh_TW.UTF-8

```
相關檔案目錄:  
/usr/share/i18n/SUPPORTED
/usr/lib/locale/
/var/lib/locales/supported.d/local
~/.bashrc
/etc/default/locale
/etc/environment
相關指令
local
local-gen
參考
locale man page
```
```
$ locale
LANG=en_US.UTF-8
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=
```

### Samba
**CentOS**: http://www.cnblogs.com/mchina/archive/2012/12/18/2816717.html  
```
# yum install samba samba-client samba-swat
# sudo vi /etc/samba/smb.conf
[global]
        workgroup = CORP
        server string = Samba Server Version %v
        netbios name = LeoSamba
        log file = /var/log/samba/log.%m
        max log size = 1000

        security = user
;       passdb backend = tdbsam

;       load printers = yes
;       cups options = raw

[share]
        comment = Share for user
        path = /home/share
        browseable = yes
        valid users = leo, guest
        public = no
        writable = yes
        read only = no
;       create mask = 0755
;       directory mask = 0755
;       printable = yes
;       create mask = 0765

```

```
# sudo vi /etc/selinux/config # SELINUX=enforcing ==> SELINUX=disable
# sudo shutdown -r now        # reload selinux configration
# sudo service iptables stop  # shutdown the fire wall
# sudo smbpasswd -a leo       # set new password 123456
# sudo smbpasswd -a guest     # set new password 123456
# sudo /etc/init.d/smb restart # Samba在Unix-like下使用的是SMB协议
# sudo /etc/init.d/nmb restart # Samba在windows下使用的是NetBIOS协议

## browse by smbclient
# smbclient //127.0.0.1/share --user leo #password 123456
 
## browse by windows or web browser
## browse \\ip\share or \\LeoSamba\share directly # username:leo, password:123456
```
 
### Tmux
**CentOS**: Install  
```shell
sudo yum install gcc kernel-devel make ncurses-devel

curl -OL https://github.com/downloads/libevent/libevent/libevent-2.0.21-stable.tar.gz
tar -xvzf libevent-2.0.21-stable.tar.gz ; cd libevent-2.0.21-stable
sudo ./configure --prefix=/usr/local
sudo make
sudo make install

curl -OL http://downloads.sourceforge.net/tmux/tmux-1.9a.tar.gz
tar -xvzf tmux-1.9a.tar.gz ; cd tmux-1.9a
LDFLAGS="-L/usr/local/lib -Wl,-rpath=/usr/local/lib" 
sudo ./configure --prefix=/usr/local
sudo make
sudo make install

## On a 32 bit system:
ln -s /usr/local/lib/libevent-2.0.so.5 /usr/lib/libevent-2.0.so.5

## On a 64 bit system:
ln -s /usr/local/lib/libevent-2.0.so.5 /usr/lib64/libevent-2.0.so.5

## Now tmux is installed
tumx -V
```
sudo vi /etc/tmux.conf

```
#此类配置可以在命令行模式中输入show-options -g查询
set-option -g base-index 1                        #窗口的初始序号；默认为0，这里设置为1
set-option -g display-time 5000                   #提示信息的持续时间；设置足够的时间以避免看不清提示，单位为毫秒
set-option -g repeat-time 1000                    #控制台激活后的持续时间；设置合适的时间以避免每次操作都要先激活控制台，单位为毫秒
set-option -g status-keys vi                      #操作状态栏时的默认键盘布局；可以设置为vi或emacs
set-option -g status-right "#(date  '+%k:%M %x')" #状态栏右方的内容；这里的设置将得到类似23:59 10/30/16的显示
set-option -g status-right-length 16              #状态栏右方的内容长度；建议把更多的空间留给状态栏左方（用于列出当前窗口）
set-option -g status-utf8 on                      开启状态栏的UTF-8支持

#此类设置可以在命令行模式中输入show-window-options -g查询
set-window-option -g mode-keys vi    #复制模式中的默认键盘布局；可以设置为vi或emacs
set-window-option -g utf8 on         #开启窗口的UTF-8支持

#将激活控制台的快捷键由Ctrl+b修改为Ctrl+a
set-option -g prefix C-a
unbind-key C-b

#Send the prefix to client inside window
#if -n is specified, it is not necessary to use the prefix key, command is bound to key alone.
bind-key -n C-x send-prefix
#So, as an example, Ctrl+a, c opens a new window in tmux; Ctrl+x, c does the same in the nested session.

#添加自定义快捷键
bind-key z kill-session                     #按z结束当前会话；相当于进入命令行模式后输入kill-session
bind-key h select-layout even-horizontal    #按h将当前面板布局切换为even-horizontal；
bind-key v select-layout even-vertical      #按v将当前面板布局切换为even-vertical；
```

`swap-window -s 3 -t 1` : let window number 3 and window number 1 swap their positions  
`swap-window -t 0` : swap the current window with the top window  
`move-window -t 0` : In the unlikely case of having no window at index 0, move the current window to top
 
You can bind that command to a key (T for "top" for example) by adding the following to your ~/.tmux.conf: `bind-key T swap-window -t 0`

### ngrok, mobi
  - [ngrok 内网穿透/映射神器 | yaosansi's blog](http://www.yaosansi.com/post/ngrok-secure-introspectable-tunnels-to-localhost/)
  - [搭建自己的ngrok服务](http://tonybai.com/2015/03/14/selfhost-ngrok-service/)
  - [Tunnel Mobi](http://www.tunnel.mobi/)  

### <a name=vim>Vi/Vim</a>
> vim 是进阶版的 vi ， vim 不但可以用不同颜色显示文字内容，还能够进行诸如 shell script, C program 等程式编辑功能， 你可以将 vim 视为一种程式编辑器!

Vim adds onto those features. Here are a some of the extended vim features:  
- Vim has been ported to a much wider range of OS's than vi.
- Vim includes support (syntax highlighting, code folding, etc) for several popular programming languages (C/C++, Python, Perl, shell, etc).
- Vim integrates with cscope.
- Vim can be used to edit files using network protocols like SSH and HTTP.
- Vim includes multilevel undo/redo.
- Vim allows the screen to be split for editing multiple files.
- Vim can edit files inside a compressed archive (gzip, zip, tar, etc).
- Vim includes a built in diff for comparing files (vimdiff).
- Vim includes support for plugins, and finer control over config and startup files.
- Vim can be scripted with vimscript, or with an external scripting language (e.g. python, perl, shell).

Config：  
```
## Conf files: /etc/vim/vimrc /etc/vim/vimrc.tiny
## Edit /etc/vim/vimrc.tiny 
set nocompatible
set backspace=2
```

> vi/vim 中可以使用 :s 命令来替换字符串

```
:s/vivian/sky/ 替换当前行第一个 vivian 为 sky
:s/vivian/sky/g 替换当前行所有 vivian 为 sky
:n,$s/vivian/sky/ 替换第 n 行开始到最后一行中每一行的第一个 vivian 为 sky
:n,$s/vivian/sky/g 替换第 n 行开始到最后一行中每一行所有 vivian 为 sky
 n 为数字，若 n 为 .，表示从当前行开始到最后一行
:%s/vivian/sky/(等同于 :g/vivian/s//sky/) 替换每一行的第一个 vivian 为 sky
:%s/vivian/sky/g(等同于 :g/vivian/s//sky/g) 替换每一行中所有 vivian 为 sky

可以使用 # 作为分隔符，此时中间出现的 / 不会作为分隔符
:s#vivian/#sky/#   替换当前行第一个 vivian/ 为 sky/
:%s+/oradata/apras/+/user01/apras1+ (使用+ 来 替换 / )： /oradata/apras/替换成/user01/apras1/
```

**Vi Common Commands** http://www.cs.colostate.edu/helpdocs/vi.html

| Flag | Command | Description |
|------|---------|-------------|
|||**To Get Into and Out Of vi**
|*|vi filename|	edit filename starting at line 1
||vi -r filename|	recover filename that was being edited when system crashed
|*|:x\<Return\>|	quit vi, writing out modified file to file named in original invocation
||:wq\<Return\>|	quit vi, writing out modified file to file named in original invocation
||:q\<Return\>|	quit (or exit) vi
|*|:q!\<Return\>|	quit vi even though latest changes have not been saved for this vi call
|||**Moving the Cursor**
|*|j or \<Return\> [or down-arrow]|   move cursor down one line
|*|k [or up-arrow]| move cursor up one line
|*|h or \<Backspace\> [or left-arrow]|    move cursor left one character
|*|l or \<Space\> [or right-arrow]|   move cursor right one character
|*|0 (zero)|    move cursor to start of current line (the one with the cursor)
|*|$|   move cursor to end of current line
||w|    move cursor to beginning of next word
||b|    move cursor back to beginning of preceding word
||:0\<Return\> or 1G| move cursor to first line in file
||:n\<Return\> or nG| move cursor to line n
||:$\<Return\> or G|  move cursor to last line in file
|||**Screen Manipulation**|
||^f|   move forward one screen|
||^b|   move backward one screen|
||^d|   move down (forward) one half screen|
||^u|   move up (back) one half screen|
||^l|   redraws the screen|
||^r|   redraws the screen, removing deleted lines|
|||**Adding, Changing, and Deleting Text**|
|*| u|undo whatever you just did; a simple toggle|
|||**Inserting or Adding Text**|
|*|i|   insert text before cursor, until \<Esc\> hit|
||I|    insert text at beginning of current line, until \<Esc\> hit|
|*|a|   append text after cursor, until \<Esc\> hit|
||A|    append text to end of current line, until \<Esc\> hit|
|*|o|   open and put text in a new line below current line, until \<Esc\> hit|
|*|O|   open and put text in a new line above current line, until \<Esc\> hit|
|||**Changing Text**|
|*|r|   replace single character under cursor (no \<Esc\> needed)|
||R|    replace characters, starting with current cursor position, until \<Esc\> hit|
||cw|   change the current word with new text, starting with the character under cursor, until \<Esc\> hit|
||cNw|  change N words beginning with character under cursor, until \<Esc\> hit; e.g., c5w changes 5 words|
||C|    change (replace) the characters in the current line, until \<Esc\> hit|
||cc|   change (replace) the entire current line, stopping when \<Esc\> is hit|
||Ncc or cNc|   change (replace) the next N lines, starting with the current line,stopping when \<Esc\> is hit|
|||**Deleting Text**|
|*|x|   delete single character under cursor|
||Nx|   delete N characters, starting with character under cursor|
||dw|   delete the single word beginning with character under cursor|
||dNw|  delete N words beginning with character under cursor; e.g., d5w deletes 5 words|
||D|    delete the remainder of the line, starting with current cursor position|
|*|dd|  delete entire current line|
||Ndd or dNd|   delete N lines, beginning with the current line; e.g., 5dd deletes 5 lines|
|||**Cutting and Pasting Text**
||yy|   copy (yank, cut) the current line into the buffer
||Nyy or yNy|   copy (yank, cut) the next N lines, including the current line, into the buffer
||p|    put (paste) the line(s) in the buffer into the text after the current line
|||**Searching Text**
||/string|  search forward for occurrence of string in text
||?string|  search backward for occurrence of string in text
||n|    move to next occurrence of search string
||N|    move to next occurrence of search string in opposite direction
|||**Determining Line Numbers**
||:.=|  returns line number of current line at bottom of screen
||:=|   returns the total number of lines at bottom of screen
||^g|   provides the current line number, along with the total number of lines,in the file at the bottom of the screen
|||**Saving and Reading Files**
||:r filename\<Return\>|  read file named filename and insert after current line (the line with cursor)
||:w\<Return\>|   write current contents to file named in original vi call
||:w newfile\<Return\>|   write current contents to a new file named newfile
||:12,35w smallfile\<Return\>|    write the contents of the lines numbered 12 through 35 to a new file named smallfile
||:w! prevfile\<Return\>| write current contents over a pre-existing file named prevfile
