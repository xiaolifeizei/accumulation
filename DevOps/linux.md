### centos修改ip地址

```bash
vi /etc/sysconfig/network-scripts/ifcfg-ens33
```

修改配置如下

```bash
BOOTPROTO=static   		#dhcp：自动分配ip ，static：静态ip
ONBOOT=yes 				#开启启动必须是yes
IPADDR=192.168.0.202    #ip地址
NETMASK=255.255.255.0   #掩码
GATEWAY=192.168.0.1     #网关
DNS1=192.168.0.1        #域名服务器1
DNS2=8.8.8.8            #域名服务器2
```

重启网络服务

```bash
systemctl restart network
```

### xshell连接不上

```bash
Connecting to 192.168.230.201:22...
Connection established.
To escape to local shell, press 'Ctrl+Alt+]'.
```

修改sshd配置文件

```bash
vim /etc/ssh/sshd_config
```

找到

```bash
#UseDns yes
```

改为

```bash
UseDns no
```

重启ssh

```bash
systemctl restart sshd
```

