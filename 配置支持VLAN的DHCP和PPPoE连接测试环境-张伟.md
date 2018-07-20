# 配置支持VLAN的测试环境拓扑

### VLAN基础环境配置

添加一个支持VLAN Tag的DHCP server，需要：

1. 添加VLAN所需要的支持802.1Q的内核模块

```shell
modprobe 8021q
```

2. 添加VLAN类型的interface

```shell
ip link add link eth1 name eth1.400 type vlan id 400
```

上面的命令在物理网卡eth1上添加了一个虚拟的vlan id为400的interface，这个interface目前还没有开启，但是已经可以通过以下命令看到：

```shell
ip link ls eth1.400
```

3. 配置VLAN interface的参数

对于ip命令来说，连接网络的基础设施是device，上面添加的虚拟网络设备就是device，拥有device之后，就可以指定Server来监听device的端口，实现收发带有VLAN Tag的数据包。

PPPoE服务器和DHCP服务器是需要指定device来运行的，不过PPPoE服务器不需要指定device的端口，也不需要device具有固定的ip地址；DHCP服务器需要device具有固定的ip地址。

首先启动对应的device：

```shell
ip link eth1.400 up
```

然后配置device的ip地址：

```shell
ip addr add 192.168.6.163 dev eth1.400
```

对于一个device，可以添加多个ip地址，也可以删除一些多余的ip地址，查看ip地址的命令如下：

```shell
ip addr show dev eth1.400
```

###  配置DHCP服务器

Linux环境下的DHCP服务器有很多，其中isc-dhcp-server是有ISC官方推出的支持全部配置以及IPv6的一款服务器，在这个过程中我们采用isc-dhcp-server来配置支持VLAN的DHCP服务器。

首先需要安装这个服务器，一般的镜像源都会有这个软件，以Ubuntu Xenial为例：

```shell
sudo apt-get install isc-dhcp-server
```

这个DHCP服务器拥有两个配置文件，分别是/etc/default/isc-dhcp-server和/etc/dhcp/dhcpd.conf，类似于nginx的配置架构，前一个文件用于配置服务器行为的相关参数，后一个文件用于配置运行时行为的相关参数。

支持VLAN Tag的DHCP服务，需要监听支持VLAN Tag的device，在/etc/default/isc-dhcp-server中可以配置我们需要监听的device，编辑配置文件，在INTERFACES选项中添加刚才配置好的eth1.500的device，注意这份配置文件采用的是空格区分的语法：

```shell
# On what interfaces should the DHCP server (dhcpd) serve DHCP requests?
#       Separate multiple interfaces with spaces, e.g. "eth0 eth1".
INTERFACES="eth1.209 eth1.500"
```

然后我们需要配置服务器的运行参数，必须配置的就是DHCP server的地址池以及网管，子网掩码等必备的参数，打开/etc/dhcp/dhcpd.conf文件进行配置，类似如下的结构：

```shell
# No service will be given on this subnet, but declaring it helps the 
# DHCP server to understand the network topology.

subnet 192.168.6.0 netmask 255.255.255.0 {
        range 192.168.6.50 192.168.6.199;
        option routers 192.168.6.163;
        option broadcast-address 192.168.6.255;
}
```

针对其余的option参数也可以配置，但是具体的名称需要参开ISC官方对应的表。

配置完成之后，启动DHCP服务器：

```shell
sudo systemctl restart isc-dhcp-server.service
```

### 配置PPPoE服务器

Linux环境下的PPPoE Server通常采用社区广泛使用的rp-pppoe，对于Ubuntu Xenial来说，安装非常简单：

```shell
sudo apt-get install ppp
```
PPPoE Server的主要配置文件为/etc/ppp/options，同时这个路径下所有的文件也都和PPPoE的认证，服务等相关。

PPPoE的认证方式主要有PAP和CHAP，CHAP在安全性上更佳，采用也更为广泛，所以我们在拓扑中也采用CHAP的方式进行认证，打开配置文件，禁用PAP，开启CHAP：

```shell
# Don't agree to authenticate using PAP.
-pap

# Require the peer to authenticate itself using CHAP [Cryptographic
# Handshake Authentication Protocol] authentication.
+chap
```

然后我们需要为PPPoE认证添加账号，PPPoE Server支持数据库和文本文件两种方式添加账号，默认采用/etc/ppp/chap-secrets这个文件中的键值对作为用户名和密码，打开文件配置用户名和密码：

```shell
# Secrets for authentication using CHAP
# client        server  secret                  IP addresses
zhang * fang *
```

根据注释的提示，第一列和第三列分别是用户名和密码，第二列和第四列是server和IP，对于PPPoE Server单实例开启的情况下，用通配符即可。

需要注意的是，PPPoE Server需要ip forwarding来进行数据包的转发，因此我们需要在Server上开启允许ip forwarding功能：

```shell
echo "1">/proc/sys/net/ipv4/ip_forward
```

配置完成之后，我们需要开启PPPoE Server，在开启时需要注意的是，我们没有在配置中添加地址池和最大连接数，这些参数都可以在运行中进行指定：

```shell
pppoe-server -I eth1.500 -L 192.168.11.1 -R 192.168.11.10 -N 10
```

上面的参数列表中，
* -I参数指定了PPPoE Server运行时接收报文的device
* -L指的是PPPoE的虚拟地址，了解PPPoE原理的话应该明白这个地址不应该和现有的device地址有冲突，PPPoE传输数据实际上是基于PPP协议，不需要使用到device本身的地址
* -R参数指定的是服务器分配给远程的客户端的IP地址，这个参数只需要配置一个起始地址即可
* -N参数配置最大连接的客户端数量，在-R参数的基础上递增

