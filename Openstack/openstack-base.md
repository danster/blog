## OpenStack

- [OpenStack Documents](http://docs.openstack.org/)
- [OpenStack Operations Guide](http://docs.openstack.org/ops-guide/)

## Network Background
---
- Internet, Ethernet
- NIC: Network Interface Card; VIF: Virtual Interface
- [TUN/TAP](https://en.wikipedia.org/wiki/TUN/TAP): are virtual network kernel devices

 ```
 TUN (namely network TUNnel) simulates a network layer device and it operates with OSI layer 3 packets like IP packets.
 TAP (namely network tap) simulates a link layer device and it operates with OSI layer 2 packets like Ethernet frames.
 TUN is used with routing, while TAP is used for creating a network bridge.Packets sent 
   by an operating system via a TUN/TAP device are delivered to a user-space program which attaches itself to the device.
 A user-space program may also pass packets into a TUN/TAP device.
 In this case the TUN/TAP device delivers (or "injects") these packets to the operating-system network stack
 thus emulating their reception from an external source.
 ```
- [TUN/TAP device driver work principle](https://www.ibm.com/developerworks/cn/linux/l-tuntap/)
- Veth device, Patch device:
- Linux Bridge, OVS, Flow Table
- **Network components**: swicth, router, firewall, load balancer
- **Tunnel technologies**: GRE, VXLAN
- **Linux network namespaces**: Linux provides namespaces for networking and processes, among other things. Each network namespace also has its own set of iptables (for both IPv4 and IPv6)
- **ip netns exec NETNS COMMAND...**: starts COMMAND running in the namespace named NETNS
- vm_1.3 ping vm_2.3:
 - vm_1.3 find mac addr of 1.1 from its arp cache.
 - if can't found, 1.3 send arp brd, 1.1 send arp response to 1.3, 1.3 send icmp request to 1.1.
 - 1.1 find the route to 2.1 from its route cache and arp cache, assume 2.1 is the next-hop of 1.1.
 - if 1.1 can't find mac addr of 2.1, 1.1 send arp brd, 2.1 send arp response to 1.1, 1.1 send icmp request to 2.1.
 - 2.1 find mac addr of 2.3 from its arp cache.
 - if can't find, 2.1 send arp brd, 2.3 send arp response to 2.1, 2.1 send icmp request to 2.3.
 - 2.3 send icmp response to 2.1, 2.1 send icmp response to 1.1, 1.1 send icmp response to 1.3.

## Neutron Network
---
- **OpenStack Networking**: API server, OpenStack Networking plug-in and agents,Messaging queue
- **Tenant networks/Provider networks**: Flat, VLAN, GRE, VXLAN
- **Network**: An isolated L2 segment, analogous to VLAN in the physical networking world.
- **Subnet**: A block of v4 or v6 IP addresses and associated configuration state.
- **Port**: A connection point for attaching a single device, such as the NIC of a virtual server, to a virtual network.  Also describes the associated network configuration, such as the MAC and IP addresses to be used on that port.
- Subnet Pools, Routers, Security groups(port level), Extensions
- **virtual network**: A Networking L2 network (identified by a UUID and optional name) whose ports can be attached as vNICs to Compute instances and to various Networking agents. The Open vSwitch and Linux Bridge plug-ins each support several different mechanisms to realize virtual networks.
- **tenant network**: A virtual network that a tenant or an administrator creates. The physical details of the network are not exposed to the tenant.
- **provider network**： A virtual network administratively created to map to a specific network in the data center, typically to enable direct access to non-OpenStack resources on that network. Tenants can be given access to provider networks.
- [**Terminology**](http://docs.openstack.org/admin-guide/networking_adv-features.html#terminology): virtual, physical, tenent, provider, local, flat, vlan, gre, vxlan network
- The ML2, Open vSwitch, and Linux Bridge plug-ins support VLAN networks, flat networks, and local networks.Only the ML2 and Open vSwitch plug-ins currently support GRE and VXLAN networks
- [OpenStack Neutron网络状态详解](http://www.openstack.cn/?p=1321 ): 介绍admin_state_up和status两个属性在Network, Port和Router上的含义

## ML2 plug-in and Agents
---
- **ML2 plug-in** is a framework allowing OpenStack Networking to simultaneously use the variety of layer 2 networking technologies found in complex real-world data centers. The ML2 framework distinguishes between the two kinds of drivers:
 - Type drivers: Flat(not for project network), VLAN, GRE, VXLAN
 - Mechanism drivers: OVS, Linux Bridge, SRIOV, MacVTap, L2 population
- An **L2 agent** serves layer 2 (Ethernet) network connectivity to OpenStack resources. It typically runs on each Network Node and on each Compute Node.
- The **L3 agent** offers advanced layer 3 services, like virtual Routers and Floating IPs. It requires an L2 agent running in parallel.
- The **DHCP agent** is responsible for DHCP and RADVD (Router Advertisement Daemon) services. It requires a running L2 agent on the same node.
- The **Metadata agent** allows instances to access cloud-init meta data and user data via the network. It requires a running L2 agent on the same node.
- The **L3 metering agent** enables layer3 traffic metering. It requires a running L3 agent on the same node.
- L2 agents support some important **security** configurations: Security Groups, Arp Spoofing Prevention

## Reference Implementation
---
The combination of a mechanism driver and an L2 agent is called ‘reference implementation’.

|Reference Implementation|Mechanism Driver|L2 agent|
|------------------|----------------|--------|
|Open vSwitch & Open vSwitch agent|Open vSwitch |Open vSwitch agent|
|Linux bridge & Linux bridge agent|Linux bridge |Linux bridge agent|

Reference implementations and other agents

|Reference Implementation|L3 agent| DHCP agent| Metadata agent| L3 Metering agent|
|------------------------|----------------|--------|---|---|
|Open vSwitch & Open vSwitch agent|yes |yes|yes |yes|
|Linux bridge & Linux bridge agent|yes |yes|yes |yes|
|SRIOV & SRIOV nic switch agent|no |no |no |no|
|MacVTap & MacVTap agent|no |no |no |no|

## Enable FWaaS

- [Neutron防火墙(FWaas)](http://fishcried.com/2016-02-05/neutron-fwaas-notes/)
- [RHEL 7: CONFIGURE FIREWALL-AS-A-SERVICE (FWAAS)](https://access.redhat.com/documentation/en/red-hat-enterprise-linux-openstack-platform/7/networking-guide/chapter-16-configure-firewall-as-a-service-fwaas)

1. Install the FWaaS packages:

 # yum install openstack-neutron-fwaas python-neutron-fwaas

2. Enable the FWaaS plugin in the `/etc/neutron/neutron.conf` file:

 service_plugins = neutron.services.firewall.fwaas_plugin.FirewallPlugin

3. Configure FWaaS in the `/etc/neutron/fwaas_driver.ini` file:

 ```
 [fwaas]
 driver = neutron.services.firewall.drivers.linux.iptables_fwaas.IptablesFwaasDriver
 enabled = True

 [service_providers]
 service_provider=LOADBALANCER:Haproxy:neutron.services.loadbalancer.drivers.haproxy.plugin_driver.HaproxyOnHostPluginDriver:default
 ```

4. FWaaS management options are available in OpenStack dashboard. Enable this option in the local_settings.py file,
 usually located on the Controller node:  

 ```
 /usr/share/openstack-dashboard/openstack_dashboard/local/local_settings.py
 'enable_firewall' = True
 ```

5. Restart neutron-server

# Open Source List

- Docker, **[Kubernate](https://github.com/kubernetes/kubernetes)**, Flynn
- Distributed Storage: **Hadoop**, Spark, HBase
- Distributed File System: **[Ceph](http://docs.ceph.org.cn/)**, XFS, **HDFS**, CIFS
- Distributed Contral: CoreOS, **[etcd](https://github.com/coreos/etcd)**, zookeeper
- Stream Processing: **[Kafka](http://kafka.apache.org/)**, Flume
- SDN: **OpenFlow**, OpenDayLight, Openvswitch
- **[OpenStack](http://docs.openstack.org/)**: Dashboard, KeyStone, Neutron, Nova, Swift, Glance
- **[FastData](http://fd.io)**
- Database: mysql, redis, mongodb, memcache


图 1： Openstack 模块关系图  
![](http://img.blog.csdn.net/20140304232125453)
