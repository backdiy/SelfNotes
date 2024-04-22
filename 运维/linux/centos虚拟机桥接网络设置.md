## 手动设置静态方式桥接网络

1.在虚拟机软件里面选择网络连接方式为桥接方式，并且选择电脑网卡。

2.输入命令`cd /etc/sysconfig/network-scripts`，编辑网络配置文件`vim enp0s3`

* 将BOOTPROTO更改为`static`
* 将ONBOOT改为`yes`
* 在文件末尾添加ip地址、子网掩码、网关地址和DNS服务器地址。注意和宿主机网络保持一致

```bash
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
BOOTPROTO="static"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="enp0s3"
UUID="1a4b0144-0165-41c9-96c6-fe3d026fa2a1"
DEVICE="enp0s3"
ONBOOT="yes"
IPADDR=192.168.194.230
NETMASK=255.255.255.0
GATEWAY=192.168.194.254
DNS1=8.8.8.8
DNS2=114.114.114.114
```

3.修改网络配置文件后重启网络服务，`nmcli c reload`