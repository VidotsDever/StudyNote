查看指定区域所有打开的端口：

```
firewall-cmd --zone=public --list-ports
```

在指定区域打开端口（记得重启防火墙）：

```
firewall-cmd --zone=public --add-port=80/tcp --permanent
```

重启防火墙：

```
firewall-cmd --reload
```

