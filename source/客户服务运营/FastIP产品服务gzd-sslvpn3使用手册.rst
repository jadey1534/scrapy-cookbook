GZD-SSLVPN3手册

第一步 创建用户
---------------

sslvpn_useradd 123456 1 123456A 1

-----123456是CID,账号会从12345601开始，第一个1表示是创建一个账号（2个账号就是2，）6000563A是组，1表示是随机密码

这个会自动生成账号密码、vrf-123456A和/etc/ocserv/config-per-group/组的配置文件

|image0|

**注意**

**如果单独为客户开账号 或者修改账号的密码 ，可以使用如下命令：-g 123456A
意思是12345601这个账号是归属于123456A这个组,会要求输入两次密码**

ocpasswd -c /etc/ocserv/ocpasswd -g **123456A** **12345601**

第二步 创建vrf
--------------

ip link add vrf-123456A type vrf table 23456

ip link set dev vrf-123456A up

**这个配置需要先贴**

vi /etc/ocserv/config-per-group/123456A

---------------脚本生成的，需要修改可以进文件里面修改。

|image1|

第三步 建立隧道，并划分至vrf
----------------------------

如果需要分配Tunnel

ip tunnel add gre17157 mode gre local 1.1.1.1 remote 2.2.2.2

ip addr add 10.10.10.0/30 dev gre17157

ip link set gre17157 alias 123456A1

ip link set gre17157 mtu 1400

ip link set gre17157 up

ip link set gre17157 master vrf-123456A1

##如果使用openvpn

vi /etc/openvpn/server/vtun17157.conf

dev-type tun

dev vtun17157----------------------------------------------tunnel号

local 121.46.28.78------------------------------------------对接IP

lport 17157---------------------------------- -------------- 端口号

ifconfig 10.30.106.233 10.30.106.234-----------------wanip

push "ping 10"

push "ping-restart 60"

push "persist-tun"

push "persist-key"

persist-tun

secret /etc/openvpn/easy-rsa/keys/openvpn.secret

writepid /var/run/openvpn-server/vtun17157.pid

status /var/run/openvpn-server/vtun17157.status 30

verb 3

topology net30

setenv vrf_tenant vrf-123456A--------------------------绑定VRF

setenv next_hop 10.30.106.234-----------------wanip

setenv vrf_table 23456------------------------------------table号

script-security 2

up /etc/openvpn/up.sh

启动tunnel

systemctl restart openvpn-server@vtun17157.service

systemctl enable openvpn-server@vtun17157.service

.. |image0| image:: /root/work/scrapy-cookbook/source/客户服务运营/FastIP产品服务/imgs/media/image1.png
   :width: 5.76319in
   :height: 1.44722in
.. |image1| image:: /root/work/scrapy-cookbook/source/客户服务运营/FastIP产品服务/imgs/media/image2.png
   :width: 5.76528in
   :height: 2.66736in
