**Keepalived**用于两台服务器之间的IP故障切换，即一台服务器宕机，可以快速切换到另一台服务器上。它的工作原理是VRRP (Virtual Router Redundancy Protocol-虚拟路由冗余协议)，首先我们要知道VRRP是一种容错性协议，它是通过将多台设备虚拟化成一台设备，如果其中一台设备出现故障，那么另一台设备可以迅速接替其工作，已保证通讯的可靠性和连续性。



[How to Setup IP Failover with KeepAlived on Ubuntu & Debian](https://tecadmin.net/setup-ip-failover-on-ubuntu-with-keepalived/)



![](D:\Github\StudyNote\assets\keepalived-vrrp-network.png)