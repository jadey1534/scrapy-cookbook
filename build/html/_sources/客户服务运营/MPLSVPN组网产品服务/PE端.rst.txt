===========
PE端
===========

   主线

   LineID:604828C1
   PE:bja-upe1
   Tunnel:Tunnel0/0/1349 #在设备上找到没有被使用过的tunnel编号，即在设备上找不到的tunnel编号
   wanip:10.30.33.100/30 
   PE 对接:10.200.200.23 #PE对接，用于PE端ipsec拨号的，每台设备用的ip都是不一样的
   CE 对接:10.50.30.5 #CE对接，用于CE端ipsec拨号的，每个客户用的都是不一样的
   PE AS号:65112
   CE AS号:65202

   拨号PE:bja-upe1
   对接IP:10.200.200.23
   LoopIP:10.50.30.5
   拨号ip:118.193.100.28
   秘钥:both-win

   #bja-upe1
   acl name CT #根据工单要求的运营商来选定
    rule permit ip source 10.200.200.23 0 destination 10.50.30.5 0 #放通用于给客户拨ipsec的ip 即配置允许进行IPSec加密的地址网段

   #bja-upe1

   #创建vpn实例，命名标准是vrf-客户cid-客户名缩写，RD和RT都是用65000：客户cid的格式
   ip vpn-instance vrf-604828-WoTai
   ipv4-family
   route-distinguisher 65000:604828
   vpn-target 65000:604828 export-extcommunity
   vpn-target 65000:604828 65000:20150721 65499:20191117 import-extcommunity



   interface Tunnel0/0/1349
   description <604828C1> Wo Tai Neng Yuan Ltd - Frankfurt Site ##Main## (28Feb2022) #命名格式：Guang Lian Ltd-Dong Guang Site ##Main##（分配数据的日期）
   bandwidth 10240 #设置网管在MIB上获取的接口带宽，并不是锁带宽的命令
   ip binding vpn-instance vrf-604828-WoTai #在tunnel中绑定vpn实例
   tcp adjust-mss 1400
   ip address 10.30.33.101 255.255.255.252
   tunnel-protocol gre #tunnel模式为GRE
   statistic enable inbound #使能接口入方向的流量统计功能
   statistic enable outbound #使能接口出方向的流量统计功能
   source 10.200.200.23 #设置tunnel源对接
   destination vpn-instance VRF-S2S-VPN 10.50.30.5 #设置tunnel目的对接，需要注意的是ipsec数据，目的对接会带一个vpn实例，VRF-S2S-VPN，而openvpn over GRE的数据中，目的对接不带vpn实例
   qos car inbound cir 10240 pir 11264 #带宽锁中的pir =（10240=*1.1）= 11264，cir是承诺信息速率，pir是指定峰值信息速率，即能够通过的最大速率
   qos car outbound cir 10240 pir 11264
   ip route-static 10.30.33.100 30 Tunnel0/0/1349 10.30.33.102 #将tunnel的wanip发布在全局路由表中


   bgp 65112
   ipv4-family vpn-instance vrf-604828-WoTai #指定的VPN实例与IPv4地址族进行关联，并进入BGP-VPN实例IPv4地址族视图。
   dampening 10 1500 3000 6000 #设置bgp路由振荡参数
   import-route direct
   import-route static route-policy bgp-To--VPN-Redistribute-Static

   #配置bgp
   peer 10.30.33.102 as-number 65202 #指定CE端的AS号
   peer 10.30.33.102 connect-interface Tunnel0/0/1349 #指定更新源接口
   peer 10.30.33.102 advertise-community #使能发送community
   peer 10.30.33.102 route-policy FROM-CE-MAIN-ROUTES import  #主线所使用的路由策略，定义从CE端学到的路由调整preferred-value为65535
   peer 10.30.33.102 route-limit 100 90 idle-timeout 5 #设置允许从对等体收到的路由数量为100，路由超限断连后，自动重新建立连接的超时定时器为90分钟



   备线

   LineID:604828C2
   PE:hkj-upe1
   Tunnel:Tunnel0/0/1162
   wanip:10.30.82.88/30
   PE 对接:10.200.200.29
   CE 对接:10.50.43.9
   PE AS号:65117
   CE AS号:65202

   拨号PE:hkj-upe1
   对接IP:10.200.200.29
   LoopIP:10.50.43.9
   拨号ip:154.18.8.116
   秘钥:both-win

   #hkj-upe1
   acl name OT
    rule permit ip source 10.200.200.29 0 destination 10.50.43.9 0

   #hkj-upe1
   ip vpn-instance vrf-604828-WoTai
   ipv4-family
   route-distinguisher 65000:604828
   vpn-target 65000:604828 export-extcommunity
   vpn-target 65000:604828 65000:20150721 65499:20191117 import-extcommunity



   interface Tunnel0/0/1162
   description <604828C2> Wo Tai Neng Yuan Ltd - Frankfurt Site ##Backup## (28Feb2022)
   bandwidth 10240
   ip binding vpn-instance vrf-604828-WoTai
   tcp adjust-mss 1400
   ip address 10.30.82.89 255.255.255.252
   tunnel-protocol gre
   statistic enable inbound
   statistic enable outbound
   source 10.200.200.29
   destination vpn-instance VRF-S2S-VPN 10.50.43.9
   qos car inbound cir 10240 pir 11264
   qos car outbound cir 10240 pir 11264
   ip route-static 10.30.82.88 30 Tunnel0/0/1162 10.30.82.90


   bgp 65117
   ipv4-family vpn-instance vrf-604828-WoTai
   dampening 10 1500 3000 6000
   import-route direct
   import-route static route-policy bgp-To--VPN-Redistribute-Static
   peer 10.30.82.90 as-number 65202
   peer 10.30.82.90 connect-interface Tunnel0/0/1162
   peer 10.30.82.90 advertise-community
   peer 10.30.82.90 route-policy FROM-CE-BACKUP-ROUTES import #备线所使用的路由策略，定义从CE端学到的路由调整local-preference为80，并打上community为65000:2222
   peer 10.30.82.90 route-limit 100 90 idle-timeout 5
