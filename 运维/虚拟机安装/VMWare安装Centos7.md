注意：网络连接是NAT模式，ONBOOT模式是yes；



网络连接：

`NAT模式(N)`：用于共享主机的IP地址；



进入控制台，输入`ip addr`查看对应网卡下是否出现ip v4地址；

如果未出现，表名无网络连接，进行如下配置：

```
进入 /etc/sysconfig/network-scripts目录，找到网卡对应的配置文件，如ifcfg-ens33,编辑如下：

TYPE=Ethernet 
PROXY_METHOD=none 
BROWSER_ONLY=no 
BOOTPROTO=dhcp 
DEFROUTE=yes 
IPV4_FAILURE_FATAL=no 
IPV6INIT=yes 
IPV6_AUTUCONF=yes 
IPV6_DEFROUTE=yes 
IPU6_FAILURE_FATAL=no 
IPV6_ADDR_GEN_MODE=stable-privacy 
AME=ens33 
UUID=29f88f2e-946-46f7-a271-948ebc48ee89 
DEVICE=ens33 
ONBOOT=yes // 这里修改成yes

```



#### 常见问题

更换WiFi环境后，如果SSH无法ping通虚拟机，可以重置网卡。

`编辑` - `虚拟网络编辑器` - `VMnet8`(选择虚拟机对应的网络连接的类型) - `还原默认设置`