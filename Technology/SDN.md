# SDN and NFV

* SDN: Software Defined Network
* NFV: Network Function Virtualization 

### SDN 网络结构：应用层，控制层，转发层

![](/images/SDN.png)

> Note: 区别于TCP\/IP的4层网络结构

* SDN是一种新型网络创新架构, 是网络虚拟化的一种实现方式, 其核心技术OpenFlow通过将网络设备控制面与数据面分离开来, 从而实现了网络流量的灵活控制, 使网络作为管道变得更加智能.
* SDN是一个框架、一种概念，结合NFV\(网络功能虚拟化\),实现了所有网络架构的大一统,是互联网的颠覆性架构
* SDN的核心思想: 把网络上所有的信息都集中到一个核心控制器\(Controller\)上处理，控制器可以针对信息编程，直接处理整体网络的逻辑
* OpenFlow是一种协议,一个标准,也即SDN的具体实践.做法很简单,将所有的报文抽出关键字段,抽象为流\(flow\),而所有流都交给Controller控制
* OpenFlow技术细节: [OpenFlow 1.3核心概念翻译与理解](http://www.anwcl.com/wordpress/openflow1-3%E6%A0%B8%E5%BF%83%E6%A6%82%E5%BF%B5%E7%BF%BB%E8%AF%91%E4%B8%8E%E7%90%86%E8%A7%A3/)

* SDN\/OpenFlow相关的开源软件: [http:\/\/www.oschina.net\/project\/tag\/452\/sdn](http://www.oschina.net/project/tag/452/sdn)

* OpenFlow Wikipedia: [https:\/\/en.wikipedia.org\/wiki\/OpenFlow](https://en.wikipedia.org/wiki/OpenFlow)


### 《SDN软件定义网络从入门到精通》导论课

* 在线课程：[http:\/\/edu.51cto.com\/course\/course\_id-4466.html](http://edu.51cto.com/course/course_id-4466.html) \(danster@**.com\/maof\***1_\*_\)
* PPT：[http:\/\/mp.weixin.qq.com\/s?\_\_biz=MjM5MTM3MzIzMg==∣=209513316&idx=1&sn=e5dbd9a2ccccb88d0ee5c4d5790699c1\#rd](http://mp.weixin.qq.com/s?__biz=MjM5MTM3MzIzMg==&mid=209513316&idx=1&sn=e5dbd9a2ccccb88d0ee5c4d5790699c1#rd)

### [ONF](https://www.opennetworking.org/index.php): Open Networking Foundation

* ONF由Google, Facebook, Alibaba等公司组件

### [ODL](https://www.opennetworking.org/index.php): OpenDayLight

* ODL由Cisco牵头组件
* **OpenDayLight**是一套社区主导的开源框架，旨在推动创新实施以及SDN透明化。面对SDN型网络，大家需要合适的工具帮助自己管理基础设施，这正是OpenDaylight的专长。作为项目核心，OpenDaylight拥有一套模块化、可插拔且极为灵活的控制器，这使其能够被部署在任何支持Java的平台之上。这款控制器中还包含一套模块合集，能够执行需要快速完成的网络任务。

![](http://static.oschina.net/uploads/img/201307/04112348_kBom.jpg =500x)

