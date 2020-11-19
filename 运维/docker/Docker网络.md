Docker的网络子系统是可插拔式的，通过驱动器来实现。

* 网桥(bridge)：默认的网络驱动器；当你的应用运行在单独的容器中，同时需要通信，这时候通常使用网桥模式；[Use bridge networks](https://docs.docker.com/network/bridge/)
* 宿主机(host)：移除容器和宿主机之间的网络隔离；容器直接使用宿主机的网络；[Use host networking](https://docs.docker.com/network/host/)