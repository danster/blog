# Tips of Ubuntu
<!-- toc -->
> these tips are about ubuntu14.04LTS-64bit trusty on thinkpad-x240 . the start operations recorded here, you can view my shell history in ~/.bash_history .

#### basic softwares and settings 

- apt-get update
- apt-get install git ssh build-essential eclispe
- config pydev and cdt on eclipse: refer to http://wiki.eclipse.org/Eclipse/Installation
```
> apt-get isntall openjdk-7-jdk
> cd /opt/ 
> tar -xvf eclipse-***-x86_64.tar.gz 
> vi /usr/share/applications/eclipse.desktop 
> ln -s /opt/eclipse/eclipse /usr/bin/eclipse
: Help>Install New Software>Add: 
: pydev: http://pydev.org/updates > PyDev
: cdt: http://download.eclipse.org/releases/kepler > Collaboration > Mylyn Context Connector:C/C++ Dev
```
- config ssh key  
```
: ssh-keygen -t rsa -C "xxx@xxx.com"
: cat ~/.ssh/id_rsa.pub > Copy-paste the key to the 'My SSH Keys' section under the 'SSH' tab in your user profile.
Please copy the complete key starting with ssh- and ending with your username and host
```
- install google pinyin for ibus then set
- set gedit > edit > Preferences > Editot > File Saving
- apt-get install nautilus-open-terminal
- vi /boot/grub/grub.cfg : set default="0"  set timeout= 0

- apt-get install chromium-browser
- add-apt-repository ppa:skunk/pepper-flash 
: Adds a repository into the /etc/apt/sources.list or /etc/apt/sources.list.d/
- apt-get update
- apt-get install pepperflashplugin-nonfree
- update-pepperflashplugin-nonfree --install


#### advanced softwares and settings	

- apt-get install python-sqlalchemy
- apt-get install python-shapely
- install tilemill   

```
: tilemill is tile map design tool and mapnik is map renderer  
: sudo ./install-tilemill.sh , mapnik is installed in it   
: adding tooltips and legends into tilemill   
```
- install pgadmin3, filezilla, QGIS, osm2pgsql  

```
: QGIS refer to http://www.qgis.org/en/site/forusers/alldownloads.html#ubuntu  
: append to /etc/app/source.list :   
  deb     http://qgis.org/debian trusty main  
  deb-src http://qgis.org/debian trusty main  
> gpg --keyserver keyserver.ubuntu.com --recv 47765B75
> gpg --export --armor 47765B75 | sudo apt-key add -
> sudo apt-get update
> sudo apt-get install qgis python-qgis qgis-plugin-grass
```

- apt-get install nginx, apache2, munin, retext
- ~/.gitconfig

```
:[alias]
:   st = status
:   lg1 = log --graph --abbrev-commit --decorate --date=relative --format=format:'%C(green)%h%C(reset) - %C(green)(%ar)%C(reset) %C(dim white)%s%C(reset) %C(dim white)- %an%C(reset)%C(yellow)%d%C(reset)' --all
:   lg2 = log --graph --abbrev-commit --decorate --format=format:'%C(green)%h%C(reset) - %C(cyan)%aD%C(reset) %C(green)(%ar)%C(reset)%C(yellow)%d%C(reset)%n''  %C(dim white)%s%C(reset) %C(dim white)- %an%C(reset)' --all
:   lg = !"git lg1"
```
- apt-cache depends build-essential
- apt-get install gpick # color pick from screen

#### nginx

1. sudo apt-get install nginx
2. change configure code (/etc/nginx/site-available/default) `listen 80 default_server; listen [::]:80 default_server ipv6only=on;
` to `listen [::]:80 default_server;`
3. lsof -i :80 
4. sudo nginx
5. wget 127.0.0.1  > welcome to nginx! success.
