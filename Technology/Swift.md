# Install Swift and KeyStone on CentOS 7
<!-- toc -->

## Design Architect

All three nodes are installed by vmware vsphere

ENV: CentOS7.1, xfs, python2.7.5

Partition: \/ 50G, \/home 30G, \/data 40G, \/opt 80G

| vm | hostname | ip | node type | installed packages |
| --- | --- | --- | --- | --- |
| 1 | swift133 | 10.32.119.133 | keystone control node, swift proxy node | keystone, mysql, swift-proxy |
| 2 | swift134 | 10.32.119.134 | swift storage node | openstack-swift-account openstack-swift-container openstack-swift-object |
| 3 | swift135 | 10.32.119.135 | swift storage node | as above |

## Preprocess

* Shutdown firewall 

```
systemctl stop firewalld
chkconfig --level 2345 firewalld off
```

* set openstack repo  

```
# Install Extra Packages for Enterprise Linux repository configuration 
yum  -y install epel-release
yum  -y update
yum repolist
# To list all available packages under epel
yum --disablerepo="*" --enablerepo="epel" list available
yum search htop
yum info htop # if found

yum  -y install chrony
yum  -y install centos-release-openstack-liberty
yum  -y upgrade
```

> you could create loacl repo for openstack : [http:\/\/blog.csdn.net\/wylfengyujiancheng\/article\/details\/50008325](http://blog.csdn.net/wylfengyujiancheng/article/details/50008325)

* install openstack packages  

swift proxy node:

```
yum install mysql mysql-server mysql-devel
chkconfig --level 2345 mysqld on
service mysqld start
yum install openstack-utils openstack-keystone python-keystoneclient
```

> 134\/135: yum -y install openstack-swift openstack-swift-account openstack-swift-container openstack-swift-object

