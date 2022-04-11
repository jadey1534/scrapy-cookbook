Cisco 单机GREoverIPSEC双线BGP
=============================

service timestamps debug datetime localtime
service timestamps log datetime localtime
service password-encryption

hostname 601249B-TaiPing-HK-Backup
logging message-counter syslog
logging buffered 102400
enable secret 5 $1$dTEa$ME6YtpXjcEa/FXTunMOHu1
aaa new-model
aaa authentication login default group tacacs+ local
aaa authentication enable default group tacacs+ enable
aaa authorization exec default group tacacs+ local
aaa authorization commands 15 default group tacacs+ local
aaa accounting exec default

aaa session-id common
clock timezone beijing 8

ip cef

username bothwin secret 5 $1$Am4N$PTMSnrn1W4y2st28nWDsH/

crypto keyring S2S_KEYRING_MAIN
pre-shared-key address 202.133.229.122 key bothwin
crypto keyring S2S_KEYRING_BACK
pre-shared-key address 114.112.236.121 key both-win

crypto isakmp policy 1
encr 3des
authentication pre-share
group 2
lifetime 28800
crypto isakmp profile S2S_profile_MAIN
keyring S2S_KEYRING_MAIN
match identity address 202.133.229.122 255.255.255.255
initiate mode aggressive # 根据接入的PE配置而定
crypto isakmp profile S2S_profile_BACK
keyring S2S_KEYRING_BACK
match identity address 114.112.236.121 255.255.255.255

crypto ipsec transform-set TS-IPsec esp-3des esp-sha-hmac
mode tunnel

ip access-list extended ACL-S2S-BACK
permit ip host 10.20.241.142 host 10.20.241.242
ip access-list extended ACL-S2S-main
permit ip host 10.10.232.140 host 10.10.232.181
ip access-list extended LAN_NETWORK

crypto map S2S-IPSEC 10 ipsec-isakmp
set peer 202.133.229.122
set transform-set TS-IPsec

set pfs group2
set isakmp-profile S2S_profile_MAIN
match address ACL-S2S-main
crypto map S2S-IPSEC 20 ipsec-isakmp
set peer 114.112.236.121
set transform-set TS-IPsec
set pfs group2
set isakmp-profile S2S_profile_BACK
match address ACL-S2S-BACK

interface Loopback0
ip address 10.10.232.140 255.255.255.255

interface Loopback1
ip address 10.20.241.142 255.255.255.255

interface FastEthernet4

no ip proxy-arp
description connenct to wan

ip address 118.143.127.188 255.255.255.240
crypto map S2S-IPSEC

interface Tunnel47167
description pri to twb-upe1
ip address 10.30.28.14 255.255.255.252
ip mtu 1500
ip flow ingress
ip flow egress
ip tcp adjust-mss 1400
load-interval 30
tunnel source Loopback0
tunnel destination 10.10.232.181

interface Tunnel65398
description backup to dgb-upe1
ip address 10.30.8.90 255.255.255.252
ip mtu 1500
ip flow ingress
ip flow egress
ip tcp adjust-mss 1400
load-interval 30
tunnel source Loopback1
tunnel destination 10.20.241.242

interface Vlan1
description to-lan

no ip proxy-arp
ip address 10.106.3.248 255.255.255.0

ip sla 1
icmp-echo 10.30.28.13 source-ip 10.30.28.14
tos 224
threshold 3000
frequency 5
ip sla schedule 1 life forever start-time now
ip sla 4
icmp-echo 10.30.8.89 source-ip 10.30.8.90
tos 224
threshold 3000
frequency 5
ip sla schedule 4 life forever start-time now

track 1 ip sla 1 reachability
delay down 30 up 10

track 4 ip sla 4 reachability
delay down 10 up 10
interface Vlan2
description to-lan
ip address 10.106.3.248 255.255.255.0

router bgp 65202
no synchronization
bgp router-id 10.30.28.14
bgp log-neighbor-changes
neighbor 10.30.8.89 remote-as 65102
neighbor 10.30.8.89 update-source Tunnel65398
neighbor 10.30.28.13 remote-as 65105
neighbor 10.30.28.13 update-source Tunnel47167
address-family ipv4
redistribute connected
redistribute static route-map bgp-To--VPN-Redistribute-Static
neighbor 10.30.8.89 activate
neighbor 10.30.8.89 send-community
neighbor 10.30.8.89 soft-reconfiguration inbound
neighbor 10.30.8.89 prefix-list Local-Main-Route out
neighbor 10.30.28.13 activate
neighbor 10.30.28.13 send-community
neighbor 10.30.28.13 soft-reconfiguration inbound
neighbor 10.30.28.13 prefix-list Local-Main-Route out
neighbor 10.30.28.13 route-map bgp-map-main in
exit-address-family

ip flow-export source Tunnel47167
ip flow-export version 5
ip flow-export destination 192.168.254.107 9995

ip access-list standard AUTH-LOGIN-IP
permit 113.105.190.147
permit 134.159.161.194
permit 120.76.31.146
permit 202.104.174.178
permit 118.143.100.106
permit 114.112.238.8 0.0.0.7
permit 10.0.0.0 0.255.255.255
permit 172.16.0.0 0.15.255.255
permit 192.168.0.0 0.0.255.255

ip route 10.10.0.0 255.255.0.0 10.106.3.254 tag 8888

ip route 0.0.0.0 0.0.0.0 FastEthernet4 118.143.127.177 230 tag 7777

ip route 10.10.232.181 255.255.255.255 FastEthernet4 118.143.127.177 tag 7777
ip route 10.20.241.242 255.255.255.255 FastEthernet4 118.143.127.177 tag 7777

ip route 114.113.245.99 255.255.255.255 Tunnel47167 10.30.28.13 tag 7777 name zabbix_pri
ip route 114.113.245.100 255.255.255.255 Tunnel65398 10.30.8.89 tag 7777 name zabbix_backup

ip route 114.112.236.121 255.255.255.255 FastEthernet4 118.143.127.177 tag 7777 name ipsecgw_pri
ip route 202.133.229.122 255.255.255.255 FastEthernet4 118.143.127.177 tag 7777 name ipsecgw_backup

ip prefix-list Local-Main-Route seq 10 permit 10.10.0.0/16
ip prefix-list Local-Main-Route seq 30 permit 10.30.28.12/30
ip prefix-list Local-Main-Route seq 40 permit 10.30.8.88/30
ip prefix-list Local-Main-Route seq 50 permit 10.106.3.0/24

route-map bgp-map-main permit 10
set local-preference 200

route-map bgp-To--VPN-Redistribute-Static deny 100
description Deny Redistribution of Static Routes to MPLS VPN
match tag 7777

route-map bgp-To--VPN-Redistribute-Static permit 110
match tag 8888
set community 65202:100

route-map bgp-To--VPN-Redistribute-Static permit 200
description Redistribute All Other Static Routes Without Tag

tacacs-server host 192.168.55.250
tacacs-server directed-request
tacacs-server key 7 00061C120C4C0208

ip tacacs source-interface loo0

line vty 0 4
access-class AUTH-LOGIN-IP in vrf-also
exec-timeout 60 0
transport input telnet ssh
line vty 5 15
access-class AUTH-LOGIN-IP in vrf-also
exec-timeout 60 0
transport input telnet ssh

ntp server 202.104.174.178
ntp server 192.168.55.250
