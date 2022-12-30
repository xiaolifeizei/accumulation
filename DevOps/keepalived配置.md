### 1、下载keepalived

下载地址：

https://www.keepalived.org/

### 2、解压缩

```bash
tar -zxvf keepalived-2.2.7.tar.gz
```

### 3、安装C编译器

```bash
yum -y install gcc-c++
```

检查

```bash
gcc -v
```

### 3、编译

```bash
./configure --prefix=/data/keepalived --sysconf=/etc
```

如果有以下提示

```bash
configure: error: 
  !!! OpenSSL is not properly installed on your system. !!!
  !!! Can not include OpenSSL headers files.            !!!
```

执行以下语句

```bash
yum -y install openssl-devel
```

编译并安装

```bash
make && make install
```

### 4、配置

添加配置文件

```bash
vim /etc/keepalived/keepalived.conf
```

配置信息如下

```nginx
! Configuration File for keepalived

#全局配置是对整个 Keepalived 生效的配置
global_defs {
   #设置 keepalived 在发生事件（比如切换）的时候，需要发送到的email地址，可以设置多个，每行一个。 
#  notification_email { 	
#    acassen@firewall.loc
#    failover@firewall.loc
#    sysadmin@firewall.loc
#  }
   #设置发送邮件通知的地址 
#  notification_email_from Alexandre.Cassen@firewall.loc
   #设置 smtp server 地址，可是ip或域名.可选端口号 （默认25）
#  smtp_server 192.168.200.1
   #设置smtp连接超时时间，单位为秒。     
#  smtp_connect_timeout 30
   #主机标识，用于邮件通知    
#  router_id LVS_DEVEL
   vrrp_skip_check_adv_addr
   #严格执行VRRP协议规范，此模式不支持节点单播
   vrrp_strict
   vrrp_garp_interval 0
   vrrp_gna_interval 0

   #指定运行脚本的用户名和组。默认使用用户的默认组。如未指定，默认为keepalived_script 用户，如无此用户，则使用root
#  script_user keepalived_script     
   #如过路径为非root可写，不要配置脚本为root用户执行。
#  enable_script_security                        
}

#VRRP 脚本声明
#vrrp_script chk_nginx_service {
    #周期性执行的脚本
#   script "/etc/keepalived/chk_nginx.sh" 
    #运行脚本的间隔时间，秒    
#   interval 3
    #权重，priority值减去此值要小于备服务的priority值
#   weight -20    
    #检测几次失败才为失败，整数    
#   fall 3        
    #检测几次状态为正常的，才确认正常，整数
#   rise 2                 
    #执行脚本的用户或组    
#   user keepalived_script                        
#}            


#vrrp 实例部分定义，VI_1自定义名称
vrrp_instance VI_1 {
    #指定 keepalived 的角色，必须大写 可选值：MASTER|BACKUP
    state BACKUP
    #网卡设置，lvs需要绑定在网卡上，realserver绑定在回环口
    #区别：lvs对访问为外，realserver为内不易暴露本机信息
    interface ens33
    #虚拟路由标识，是一个数字，同一个vrrp实例使用唯一的标识
    #MASTER和BACKUP的同一个vrrp_instance下这个标识必须保持一致
    virtual_router_id 51
    #定义优先级，数字越大，优先级越高
    priority 100
    #设定 MASTER 与 BACKUP 负载均衡之间同步检查的时间间隔，单位为秒，两个节点设置必须一样
    advert_int 1
    
    #设置 nopreempt 防止抢占资源
    nopreempt         
    #设置验证类型和密码，两个节点必须一致    
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    #设置虚拟IP地址，可以设置多个虚拟IP地址，每行一个
    virtual_ipaddress {
        192.168.230.222
    }
    
    #脚本监控状态
#    track_script {   
        #可加权重，但会覆盖声明的脚本权重值。chk_nginx_service weight -20
#        chk_nginx_service                         
#    }
    
    #当前节点成为master时，通知脚本执行任务
#   notify_master "/etc/keepalived/start_haproxy.sh start"  
    #当前节点成为backup时，通知脚本执行任务
#   notify_backup "/etc/keepalived/start_haproxy.sh stop"   
    #当当前节点出现故障，执行的任务;
#   notify_fault  "/etc/keepalived/start_haproxy.sh stop"   
}

#定义RealServer对应的VIP及服务端口，IP和端口之间用空格隔开
virtual_server 192.168.230.222 8066 {    
    #每隔6秒查询realserver状态
    delay_loop 6   
    #后端调试算法（load balancing algorithm）
    lb_algo rr         
    #LVS调度类型NAT/DR/TUN
    lb_kind DR         
        
    #同一IP的连接60秒内被分配到同一台realserver
    #persistence_timeout 60     
    #用TCP协议检查realserver状态    
    protocol TCP          
        
    real_server 192.168.230.201 8066 {    
        #权重，最大越高，lvs就越优先访问
        weight 1      
        notify_down "systemctl stop keepalived"
        #keepalived的健康检查方式HTTP_GET | SSL_GET | TCP_CHECK | SMTP_CHECK | MISC
        TCP_CHECK {    
            #2秒无响应超时
            connect_timeout 2  
            #重连次数3次    
            retry 3           
            #重连间隔时间
            delay_before_retry 3  
            #健康检查realserver的端口
            connect_port 8066                   
        }                                     
    }                                         
}

```

备机配置文件

```nginx
! Configuration File for keepalived

global_defs {
   vrrp_skip_check_adv_addr
   vrrp_strict
   vrrp_garp_interval 0
   vrrp_gna_interval 0                      
}


vrrp_instance VI_1 {
    state BACKUP
    interface ens33
    virtual_router_id 51
    priority 90
    advert_int 1 
    nopreempt
    authentication {
        auth_type PASS
        auth_pass 1111
    }

    virtual_ipaddress {
        192.168.230.222
    }
}

virtual_server 192.168.230.222 8066 {    
    delay_loop 6   
    lb_algo rr         
    lb_kind DR         
         
    protocol TCP          
                                            
    real_server 192.168.230.200 8066 {          
        weight 1     
        notify_down "systemctl stop keepalived"
        TCP_CHECK {                           
            connect_timeout 2                
            retry 3                           
            delay_before_retry 3              
            connect_port 8066               
        }
    }
}
```

#### 5、启动

```bash
systemctl start keepalived
```

### 6、查看虚拟ip是否添加成功

```bash
ip addr
```

### 7、设置开机启动

```bash
systemctl enable keepalived
```

