# 双网卡 UBUNTU/树莓派通过NAT方式共享网络

* 设备情况：PC或者树莓派，两张以太网卡，一张主板自带，一张USB网卡
* 网络设置：网卡eth0连接外网，网卡eth1连接内网
* 目的：使eth0的网络共享给eth1，使eth1连接的内网环境的其他设备可以上外网
* IP设置：eth0无需设置，使用原本的设置即可（DHCP或者静态IP）；eth1设置IP：192.168.0.1，子网掩码：255.255.255.0

## UBUNTU 设置IP

> ​		在UBUNTU中使用命令行修改`sudo vi /etc/network/interface`，如果eth0网卡可以连接外网，这里就不去修改eth0的信息，如果不能连接外网，需先解决连接外网问题后再修改该文件。
>
> ​		修改eth1的IP信息：
>
> ​		`auto eth1`
>
> ​		`iface eth1 inet static`
>
> ​		`address 192.168.0.1`
>
> ​		`netmask 255.255.255.0`	

## 树莓派设置IP

>  		再树莓派中使用命令行修改`sudo vi /etc/dhcpcd.conf`
>
> ​		添加或者修改eth1的IP信息：
>
> ​		`interface eth1`
>
> ​		`static ip_address=192.168.0.1/24`

## 打开转发开关

> * 方法一：使用命令`sudo echo 1 >/proc/sys/net/ipv4/ip_forward`，该方法只能生效一次，重启后失效
> * 方法二：修改sysctl.conf文件`sudo vi /etc/sysctl.conf`，将net.ipv4.ip_forward=1前面的#注释去掉，然后执行`sudo sysctl -p`

## 修改转发规则

> 使用命令：
>
> `iptables -F`
>
> `iptables -P INPUT ACCEPT`
>
> `iptables -P FORWARD ACCEPT`
>
> `iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -o eth0 -j MASQUERADE`
>
> 重启后同样会失效，所以需要把这四行命令添加到启动项文件`rc.local`中，（注：添加在`exit 0`之前）