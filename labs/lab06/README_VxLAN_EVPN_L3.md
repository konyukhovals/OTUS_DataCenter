Hometask 2.12 - VxLAN EVPN L3 - Arista 4.29.2F
Перешел на Аристу, не смог в Cisco Nexus нормально запустить VxLAN EVPN

# Наша топология

<img width="1369" height="863" alt="image" src="https://github.com/user-attachments/assets/6ed271e8-2004-4618-a832-bbaf46b8c4dd" />

# Наша адресация

Адресация лупбеков

| Устройство | Loopback0 (Underlay) |	Loopback1 (Overlay) |
|:-----------|:---------------------|:--------------------|
|Spine 1|10.1.1.0/32|10.255.1.0/32|
|Spine 2|10.1.1.1/32|10.255.1.1/32|
|Leaf 1|10.1.1.10/32|10.255.1.10/32|
|Leaf 2|10.1.1.11/32|10.255.1.11/32|
|Leaf 3|10.1.1.12/32|10.255.1.12/32|

Адресация p2p-подсетей /31, от первого адреса подсети от каждого спайна к лифу

| Устройство | Устройство |	Подсеть |
|:-----------|:---------------------|:--------------------|
|Spine 1|Leaf 1|10.1.0.0/31|
|Spine 1|Leaf 2|10.1.0.2/31|
|Spine 1|Leaf 3|10.1.0.4/31|
|||
|Spine 2|Leaf 1|10.1.0.8/31|
|Spine 2|Leaf 2|10.1.0.10/31|
|Spine 2|Leaf 3|10.1.0.12/31|

# Первичный конфиг Arista

```
admin

en
conf t
zerotouch cancel

enable
configure terminal
service routing protocols model multi-agent
end
write memory
reload
```

# Демонстрация работы схемы

Для демонстрации сделано 2 VxLAN, 10010 И 10020, ассоциированы с VLAN 10 И 20. Основные шлюзы на int vlan 10/20 продублированы на каждом Leaf (одинаковый IP), и размещены в vrf TENANT-A. Хост из Vlan 10 может пинговать хост в Vlan 20 - на хостах теперь настроен IP, mask, default gateway

<img width="742" height="314" alt="image" src="https://github.com/user-attachments/assets/2b67dae5-6189-43ac-a41b-ba5b24427aef" />

Святой и невинный пинг - теперь он таскается через NVI50000
<img width="947" height="740" alt="изображение" src="https://github.com/user-attachments/assets/70db6962-4862-46a8-933c-4c257dd8c49d" />


Чего только BGP с собой не таскает
<img width="938" height="809" alt="изображение" src="https://github.com/user-attachments/assets/03a33a3b-02e6-428d-9ef3-18157b742920" />
<img width="903" height="1061" alt="изображение" src="https://github.com/user-attachments/assets/cf15e6ee-1aa6-457a-ad11-2dbf3d97f6e1" />

Набор команд для диагностики (sh mac add - для Leaf)

```
show ip bgp summary
show bgp evpn summary
show bgp evpn route-type imet
show bgp evpn route-type mac-ip
show bgp evpn route-type ip-prefix
show vxlan vni
show vxlan vtep
show ip route vrf TENANT-A
sh mac address-table
```

```
Spine1#show ip bgp summary
BGP summary information for VRF default
Router identifier 10.1.1.0, local AS number 65000
Neighbor Status Codes: m - Under maintenance
  Neighbor    V AS           MsgRcvd   MsgSent  InQ OutQ  Up/Down State   PfxRcd PfxAcc
  10.1.0.1    4 65210            306       305    0    0 04:06:18 Estab   4      4
  10.1.0.3    4 65211            306       298    0    0 04:06:01 Estab   4      4
  10.1.0.5    4 65212            298       300    0    0 04:05:41 Estab   4      4
  10.255.1.10 4 65210            356       326    0    0 04:06:17 Estab   4      4
  10.255.1.11 4 65211            340       360    0    0 04:06:00 Estab   4      4
  10.255.1.12 4 65212            348       337    0    0 04:05:40 Estab   4      4
Spine1#
Spine1#show bgp evpn summary
BGP summary information for VRF default
Router identifier 10.1.1.0, local AS number 65000
Neighbor Status Codes: m - Under maintenance
  Neighbor    V AS           MsgRcvd   MsgSent  InQ OutQ  Up/Down State   PfxRcd PfxAcc
  10.255.1.10 4 65210            356       326    0    0 04:06:17 Estab   8      8
  10.255.1.11 4 65211            340       360    0    0 04:06:00 Estab   4      4
  10.255.1.12 4 65212            348       337    0    0 04:05:40 Estab   7      7
Spine1#
Spine1#show bgp evpn route-type imet
BGP routing table information for VRF default
Router identifier 10.1.1.0, local AS number 65000
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
 * >      RD: 10.1.1.10:10 imet 10.255.1.10
                                 10.255.1.10           -       100     0       65210 i
 * >      RD: 10.1.1.10:20 imet 10.255.1.10
                                 10.255.1.10           -       100     0       65210 i
 * >      RD: 10.1.1.11:10 imet 10.255.1.11
                                 10.255.1.11           -       100     0       65211 i
 * >      RD: 10.1.1.11:20 imet 10.255.1.11
                                 10.255.1.11           -       100     0       65211 i
 * >      RD: 10.1.1.12:10 imet 10.255.1.12
                                 10.255.1.12           -       100     0       65212 i
 * >      RD: 10.1.1.12:20 imet 10.255.1.12
                                 10.255.1.12           -       100     0       65212 i
Spine1#
Spine1#show bgp evpn route-type mac-ip
BGP routing table information for VRF default
Router identifier 10.1.1.0, local AS number 65000
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
 * >      RD: 10.1.1.10:10 mac-ip 0050.7966.6801
                                 10.255.1.10           -       100     0       65210 i
 * >      RD: 10.1.1.10:10 mac-ip 0050.7966.6801 192.168.10.1
                                 10.255.1.10           -       100     0       65210 i
 * >      RD: 10.1.1.12:10 mac-ip 0050.7966.6802
                                 10.255.1.12           -       100     0       65212 i
 * >      RD: 10.1.1.10:20 mac-ip 0050.7966.6803
                                 10.255.1.10           -       100     0       65210 i
 * >      RD: 10.1.1.10:20 mac-ip 0050.7966.6803 192.168.20.1
                                 10.255.1.10           -       100     0       65210 i
 * >      RD: 10.1.1.12:20 mac-ip 0050.7966.6804
                                 10.255.1.12           -       100     0       65212 i
 * >      RD: 10.1.1.12:20 mac-ip 0050.7966.6804 192.168.20.2
                                 10.255.1.12           -       100     0       65212 i
Spine1#
Spine1#show bgp evpn route-type ip-prefix
% Incomplete command
Spine1#
Spine1#show vxlan vni
Spine1#
Spine1#show vxlan vtep
Spine1#
Spine1#show ip route vrf TENANT-A
% IP Routing table for VRF TENANT-A does not exist.
Spine1#
Spine1#sh mac address-table
          Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports      Moves   Last Move
----    -----------       ----        -----      -----   ---------
Total Mac Addresses for this criterion: 0

          Multicast Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       ----        -----
Total Mac Addresses for this criterion: 0
Spine1#
Spine1#
```

Выдача с Leaf2 самая интересная, поскольку на нем нет хостов, а вот инфа в оверлее есть
```
Leaf2#
Leaf2#show ip bgp summary
BGP summary information for VRF default
Router identifier 10.1.1.11, local AS number 65211
Neighbor Status Codes: m - Under maintenance
  Neighbor   V AS           MsgRcvd   MsgSent  InQ OutQ  Up/Down State   PfxRcd PfxAcc
  10.1.0.2   4 65000            304       311    0    0 04:10:40 Estab   11     11
  10.1.0.8   4 65000            306       310    0    0 04:10:40 Estab   11     11
  10.255.1.0 4 65000            365       345    0    0 04:10:39 Estab   11     11
  10.255.1.1 4 65000            365       352    0    0 04:10:38 Estab   11     11
Leaf2#
Leaf2#show bgp evpn summary
BGP summary information for VRF default
Router identifier 10.1.1.11, local AS number 65211
Neighbor Status Codes: m - Under maintenance
  Neighbor   V AS           MsgRcvd   MsgSent  InQ OutQ  Up/Down State   PfxRcd PfxAcc
  10.255.1.0 4 65000            365       345    0    0 04:10:39 Estab   15     15
  10.255.1.1 4 65000            365       352    0    0 04:10:39 Estab   15     15
Leaf2#

Leaf2#show bgp evpn route-type imet
BGP routing table information for VRF default
Router identifier 10.1.1.11, local AS number 65211
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
 * >Ec    RD: 10.1.1.10:10 imet 10.255.1.10
                                 10.255.1.10           -       100     0       65000 65210 i
 *  ec    RD: 10.1.1.10:10 imet 10.255.1.10
                                 10.255.1.10           -       100     0       65000 65210 i
 * >Ec    RD: 10.1.1.10:20 imet 10.255.1.10
                                 10.255.1.10           -       100     0       65000 65210 i
 *  ec    RD: 10.1.1.10:20 imet 10.255.1.10
                                 10.255.1.10           -       100     0       65000 65210 i
 * >      RD: 10.1.1.11:10 imet 10.255.1.11
                                 -                     -       -       0       i
 * >      RD: 10.1.1.11:20 imet 10.255.1.11
                                 -                     -       -       0       i
 * >Ec    RD: 10.1.1.12:10 imet 10.255.1.12
                                 10.255.1.12           -       100     0       65000 65212 i
 *  ec    RD: 10.1.1.12:10 imet 10.255.1.12
                                 10.255.1.12           -       100     0       65000 65212 i
 * >Ec    RD: 10.1.1.12:20 imet 10.255.1.12
                                 10.255.1.12           -       100     0       65000 65212 i
 *  ec    RD: 10.1.1.12:20 imet 10.255.1.12
                                 10.255.1.12           -       100     0       65000 65212 i
Leaf2#


Leaf2#show bgp evpn route-type mac-ip
BGP routing table information for VRF default
Router identifier 10.1.1.11, local AS number 65211
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
 * >Ec    RD: 10.1.1.10:10 mac-ip 0050.7966.6801
                                 10.255.1.10           -       100     0       65000 65210 i
 *  ec    RD: 10.1.1.10:10 mac-ip 0050.7966.6801
                                 10.255.1.10           -       100     0       65000 65210 i
 * >Ec    RD: 10.1.1.10:10 mac-ip 0050.7966.6801 192.168.10.1
                                 10.255.1.10           -       100     0       65000 65210 i
 *  ec    RD: 10.1.1.10:10 mac-ip 0050.7966.6801 192.168.10.1
                                 10.255.1.10           -       100     0       65000 65210 i
 * >Ec    RD: 10.1.1.12:10 mac-ip 0050.7966.6802
                                 10.255.1.12           -       100     0       65000 65212 i
 *  ec    RD: 10.1.1.12:10 mac-ip 0050.7966.6802
                                 10.255.1.12           -       100     0       65000 65212 i
 * >Ec    RD: 10.1.1.10:20 mac-ip 0050.7966.6803
                                 10.255.1.10           -       100     0       65000 65210 i
 *  ec    RD: 10.1.1.10:20 mac-ip 0050.7966.6803
                                 10.255.1.10           -       100     0       65000 65210 i
 * >Ec    RD: 10.1.1.10:20 mac-ip 0050.7966.6803 192.168.20.1
                                 10.255.1.10           -       100     0       65000 65210 i
 *  ec    RD: 10.1.1.10:20 mac-ip 0050.7966.6803 192.168.20.1
                                 10.255.1.10           -       100     0       65000 65210 i
 * >Ec    RD: 10.1.1.12:20 mac-ip 0050.7966.6804
                                 10.255.1.12           -       100     0       65000 65212 i
 *  ec    RD: 10.1.1.12:20 mac-ip 0050.7966.6804
                                 10.255.1.12           -       100     0       65000 65212 i
 * >Ec    RD: 10.1.1.12:20 mac-ip 0050.7966.6804 192.168.20.2
                                 10.255.1.12           -       100     0       65000 65212 i
 *  ec    RD: 10.1.1.12:20 mac-ip 0050.7966.6804 192.168.20.2
                                 10.255.1.12           -       100     0       65000 65212 i
Leaf2#

Leaf2#
Leaf2#show bgp evpn route-type ip-prefix
% Incomplete command
Leaf2#
Leaf2#show vxlan vni
VNI to VLAN Mapping for Vxlan1
VNI         VLAN       Source       Interface       802.1Q Tag
----------- ---------- ------------ --------------- ----------
10100       10         static       Vxlan1          10        
10200       20         static       Vxlan1          20        

VNI to dynamic VLAN Mapping for Vxlan1
VNI         VLAN       VRF            Source       
----------- ---------- -------------- ------------ 
50000       4094       TENANT-A       evpn         

Leaf2#
Leaf2#show vxlan vtep
Remote VTEPS for Vxlan1:

VTEP              Tunnel Type(s)
----------------- --------------
10.255.1.10       unicast, flood
10.255.1.12       unicast, flood

Total number of remote VTEPS:  2
Leaf2#

Leaf2#show ip route vrf TENANT-A
VRF: TENANT-A
Codes: C - connected, S - static, K - kernel, 
       O - OSPF, IA - OSPF inter area, E1 - OSPF external type 1,
       E2 - OSPF external type 2, N1 - OSPF NSSA external type 1,
       N2 - OSPF NSSA external type2, B - Other BGP Routes,
       B I - iBGP, B E - eBGP, R - RIP, I L1 - IS-IS level 1,
       I L2 - IS-IS level 2, O3 - OSPFv3, A B - BGP Aggregate,
       A O - OSPF Summary, NG - Nexthop Group Static Route,
       V - VXLAN Control Service, M - Martian,
       DH - DHCP client installed default route,
       DP - Dynamic Policy Route, L - VRF Leaked,
       G  - gRIBI, RC - Route Cache Route

Gateway of last resort is not set

 B E      192.168.10.1/32 [200/0] via VTEP 10.255.1.10 VNI 50000 router-mac 50:ff:71:37:dd:a5 local-interface Vxlan1
 C        192.168.10.0/24 is directly connected, Vlan10
 B E      192.168.20.1/32 [200/0] via VTEP 10.255.1.10 VNI 50000 router-mac 50:ff:71:37:dd:a5 local-interface Vxlan1
 B E      192.168.20.2/32 [200/0] via VTEP 10.255.1.12 VNI 50000 router-mac 50:4e:16:08:a4:05 local-interface Vxlan1
 C        192.168.20.0/24 is directly connected, Vlan20
Leaf2#
Leaf2#sh mac address-table
          Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports      Moves   Last Move
----    -----------       ----        -----      -----   ---------
   1    0001.0001.0001    STATIC      Cpu
  10    0001.0001.0001    STATIC      Cpu
  10    0050.7966.6801    DYNAMIC     Vx1        1       0:04:47 ago
  10    0050.7966.6802    DYNAMIC     Vx1        1       0:04:42 ago
  20    0001.0001.0001    STATIC      Cpu
  20    0050.7966.6803    DYNAMIC     Vx1        1       0:04:45 ago
  20    0050.7966.6804    DYNAMIC     Vx1        1       0:04:47 ago
4094    0001.0001.0001    STATIC      Cpu
4094    504e.1608.a405    DYNAMIC     Vx1        1       0:36:08 ago
4094    50ff.7137.dda5    DYNAMIC     Vx1        1       0:35:55 ago
Total Mac Addresses for this criterion: 10

          Multicast Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       ----        -----
Total Mac Addresses for this criterion: 0
Leaf2#

```



## Spine1

```
Spine1#
Spine1#sh run
! Command: show running-config
! device: Spine1 (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Spine1
!
spanning-tree mode mstp
!
interface Ethernet1
   no switchport
   ip address 10.1.0.0/31
!
interface Ethernet2
   no switchport
   ip address 10.1.0.2/31
!
interface Ethernet3
   no switchport
   ip address 10.1.0.4/31
!
interface Ethernet4
!
interface Ethernet5
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Loopback0
   ip address 10.1.1.0/32
!
interface Loopback1
   ip address 10.255.1.0/32
!
interface Management1
!
ip routing
!
router bgp 65000
   router-id 10.1.1.0
   maximum-paths 4
   neighbor 10.1.0.1 remote-as 65210
   neighbor 10.1.0.3 remote-as 65211
   neighbor 10.1.0.5 remote-as 65212
   neighbor 10.255.1.10 remote-as 65210
   neighbor 10.255.1.10 next-hop-unchanged
   neighbor 10.255.1.10 update-source Loopback1
   neighbor 10.255.1.10 ebgp-multihop 3
   neighbor 10.255.1.10 send-community extended
   neighbor 10.255.1.11 remote-as 65211
   neighbor 10.255.1.11 next-hop-unchanged
   neighbor 10.255.1.11 update-source Loopback1
   neighbor 10.255.1.11 ebgp-multihop 3
   neighbor 10.255.1.11 send-community extended
   neighbor 10.255.1.12 remote-as 65212
   neighbor 10.255.1.12 next-hop-unchanged
   neighbor 10.255.1.12 update-source Loopback1
   neighbor 10.255.1.12 ebgp-multihop 3
   neighbor 10.255.1.12 send-community extended
   !
   address-family evpn
      neighbor 10.255.1.10 activate
      neighbor 10.255.1.11 activate
      neighbor 10.255.1.12 activate
   !
   address-family ipv4
      neighbor 10.1.0.1 activate
      neighbor 10.1.0.3 activate
      neighbor 10.1.0.5 activate
      network 10.1.0.0/31
      network 10.1.0.2/31
      network 10.1.0.4/31
      network 10.1.1.0/32
      network 10.255.1.0/32
!
end
Spine1#

```

## Spine2

```
Spine2#sh run
! Command: show running-config
! device: Spine2 (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Spine2
!
spanning-tree mode mstp
!
interface Ethernet1
   no switchport
   ip address 10.1.0.6/31
!
interface Ethernet2
   no switchport
   ip address 10.1.0.8/31
!
interface Ethernet3
   no switchport
   ip address 10.1.0.10/31
!
interface Ethernet4
!
interface Ethernet5
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Loopback0
   ip address 10.1.1.1/32
!
interface Loopback1
   ip address 10.255.1.1/32
!
interface Management1
!
ip routing
!
router bgp 65000
   router-id 10.1.1.1
   maximum-paths 4
   neighbor 10.1.0.7 remote-as 65210
   neighbor 10.1.0.9 remote-as 65211
   neighbor 10.1.0.11 remote-as 65212
   neighbor 10.255.1.10 remote-as 65210
   neighbor 10.255.1.10 next-hop-unchanged
   neighbor 10.255.1.10 update-source Loopback1
   neighbor 10.255.1.10 ebgp-multihop 3
   neighbor 10.255.1.10 send-community extended
   neighbor 10.255.1.11 remote-as 65211
   neighbor 10.255.1.11 next-hop-unchanged
   neighbor 10.255.1.11 update-source Loopback1
   neighbor 10.255.1.11 ebgp-multihop 3
   neighbor 10.255.1.11 send-community extended
   neighbor 10.255.1.12 remote-as 65212
   neighbor 10.255.1.12 next-hop-unchanged
   neighbor 10.255.1.12 update-source Loopback1
   neighbor 10.255.1.12 ebgp-multihop 3
   neighbor 10.255.1.12 send-community extended
   !
   address-family evpn
      neighbor 10.255.1.10 activate
      neighbor 10.255.1.11 activate
      neighbor 10.255.1.12 activate
   !
   address-family ipv4
      neighbor 10.1.0.7 activate
      neighbor 10.1.0.9 activate
      neighbor 10.1.0.11 activate
      network 10.1.0.6/31
      network 10.1.0.8/31
      network 10.1.0.10/31
      network 10.1.1.1/32
      network 10.255.1.1/32
!
end
Spine2#

```

## Leaf1

```
Leaf1#
Leaf1#sh run
! Command: show running-config
! device: Leaf1 (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Leaf1
!
spanning-tree mode mstp
!
vlan 10,20
!
vrf instance TENANT-A
!
interface Ethernet1
   no switchport
   ip address 10.1.0.1/31
!
interface Ethernet2
   no switchport
   ip address 10.1.0.7/31
!
interface Ethernet3
!
interface Ethernet4
!
interface Ethernet5
!
interface Ethernet6
!
interface Ethernet7
   switchport access vlan 10
   spanning-tree portfast
!
interface Ethernet8
   switchport access vlan 20
   spanning-tree portfast
!
interface Loopback0
   ip address 10.1.1.10/32
!
interface Loopback1
   ip address 10.255.1.10/32
!
interface Management1
!
interface Vlan10
   no autostate
   vrf TENANT-A
   ip address virtual 192.168.10.254/24
!
interface Vlan20
   no autostate
   vrf TENANT-A
   ip address virtual 192.168.20.254/24
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 10100
   vxlan vlan 20 vni 10200
   vxlan vrf TENANT-A vni 50000
!
ip virtual-router mac-address 00:01:00:01:00:01
!
ip routing
ip routing vrf TENANT-A
!
ip route vrf TENANT-A 192.168.10.0/24 Null0
ip route vrf TENANT-A 192.168.20.0/24 Null0
!
router bgp 65210
   router-id 10.1.1.10
   rd auto
   maximum-paths 4
   neighbor 10.1.0.0 remote-as 65000
   neighbor 10.1.0.6 remote-as 65000
   neighbor 10.255.1.0 remote-as 65000
   neighbor 10.255.1.0 update-source Loopback1
   neighbor 10.255.1.0 ebgp-multihop 3
   neighbor 10.255.1.0 send-community extended
   neighbor 10.255.1.1 remote-as 65000
   neighbor 10.255.1.1 update-source Loopback1
   neighbor 10.255.1.1 ebgp-multihop 3
   neighbor 10.255.1.1 send-community extended
   redistribute connected
   !
   vlan 10
      rd auto
      route-target both 10100:10100
      redistribute learned
   !
   vlan 20
      rd auto
      route-target both 10200:10200
      redistribute learned
   !
   address-family evpn
      neighbor 10.255.1.0 activate
      neighbor 10.255.1.1 activate
   !
   address-family ipv4
      neighbor 10.1.0.0 activate
      neighbor 10.1.0.6 activate
      network 10.1.0.0/31
      network 10.1.0.6/31
      network 10.1.1.10/32
      network 10.255.1.10/32
   !
   vrf TENANT-A
      rd 10.255.1.10:50000
      route-target import 50000:50000
      route-target export 50000:50000
      redistribute connected
!
end
Leaf1#
```

## Leaf2

```
Leaf2#
Leaf2#sh run
! Command: show running-config
! device: Leaf2 (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Leaf2
!
spanning-tree mode mstp
!
vlan 10,20
!
vrf instance TENANT-A
!
interface Ethernet1
   no switchport
   ip address 10.1.0.3/31
!
interface Ethernet2
   no switchport
   ip address 10.1.0.9/31
!
interface Ethernet3
!
interface Ethernet4
!
interface Ethernet5
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Loopback0
   ip address 10.1.1.11/32
!
interface Loopback1
   ip address 10.255.1.11/32
!
interface Management1
!
interface Vlan10
   no autostate
   vrf TENANT-A
   ip address virtual 192.168.10.254/24
!
interface Vlan20
   no autostate
   vrf TENANT-A
   ip address virtual 192.168.20.254/24
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 10100
   vxlan vlan 20 vni 10200
   vxlan vrf TENANT-A vni 50000
!
ip virtual-router mac-address 00:01:00:01:00:01
!
ip routing
ip routing vrf TENANT-A
!
ip route vrf TENANT-A 192.168.10.0/24 Null0
ip route vrf TENANT-A 192.168.20.0/24 Null0
!
router bgp 65211
   router-id 10.1.1.11
   rd auto
   maximum-paths 4
   neighbor 10.1.0.2 remote-as 65000
   neighbor 10.1.0.8 remote-as 65000
   neighbor 10.255.1.0 remote-as 65000
   neighbor 10.255.1.0 update-source Loopback1
   neighbor 10.255.1.0 ebgp-multihop 3
   neighbor 10.255.1.0 send-community extended
   neighbor 10.255.1.1 remote-as 65000
   neighbor 10.255.1.1 update-source Loopback1
   neighbor 10.255.1.1 ebgp-multihop 3
   neighbor 10.255.1.1 send-community extended
   redistribute connected
   !
   vlan 10
      rd auto
      route-target both 10100:10100
      redistribute learned
   !
   vlan 20
      rd auto
      route-target both 10200:10200
      redistribute learned
   !
   address-family evpn
      neighbor 10.255.1.0 activate
      neighbor 10.255.1.1 activate
   !
   address-family ipv4
      neighbor 10.1.0.2 activate
      neighbor 10.1.0.8 activate
      network 10.1.0.2/31
      network 10.1.0.8/31
      network 10.1.1.11/32
      network 10.255.1.11/32
   !
   vrf TENANT-A
      rd 10.255.1.11:50000
      route-target import 50000:50000
      route-target export 50000:50000
      redistribute connected
!
end
Leaf2#
```

## Leaf3

```
Leaf3#
Leaf3#sh run
! Command: show running-config
! device: Leaf3 (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Leaf3
!
spanning-tree mode mstp
!
vlan 10,20
!
vrf instance TENANT-A
!
interface Ethernet1
   no switchport
   ip address 10.1.0.5/31
!
interface Ethernet2
   no switchport
   ip address 10.1.0.11/31
!
interface Ethernet3
!
interface Ethernet4
!
interface Ethernet5
!
interface Ethernet6
!
interface Ethernet7
   switchport access vlan 10
   spanning-tree portfast
!
interface Ethernet8
   switchport access vlan 20
   spanning-tree portfast
!
interface Loopback0
   ip address 10.1.1.12/32
!
interface Loopback1
   ip address 10.255.1.12/32
!
interface Management1
!
interface Vlan10
   no autostate
   vrf TENANT-A
   ip address virtual 192.168.10.254/24
!
interface Vlan20
   no autostate
   vrf TENANT-A
   ip address virtual 192.168.20.254/24
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 10100
   vxlan vlan 20 vni 10200
   vxlan vrf TENANT-A vni 50000
!
ip virtual-router mac-address 00:01:00:01:00:01
!
ip routing
ip routing vrf TENANT-A
!
ip route vrf TENANT-A 192.168.10.0/24 Null0
ip route vrf TENANT-A 192.168.20.0/24 Null0
!
router bgp 65212
   router-id 10.1.1.12
   rd auto
   maximum-paths 4
   neighbor 10.1.0.4 remote-as 65000
   neighbor 10.1.0.10 remote-as 65000
   neighbor 10.255.1.0 remote-as 65000
   neighbor 10.255.1.0 update-source Loopback1
   neighbor 10.255.1.0 ebgp-multihop 3
   neighbor 10.255.1.0 send-community extended
   neighbor 10.255.1.1 remote-as 65000
   neighbor 10.255.1.1 update-source Loopback1
   neighbor 10.255.1.1 ebgp-multihop 3
   neighbor 10.255.1.1 send-community extended
   redistribute connected
   !
   vlan 10
      rd auto
      route-target both 10100:10100
      redistribute learned
   !
   vlan 20
      rd auto
      route-target both 10200:10200
      redistribute learned
   !
   address-family evpn
      neighbor 10.255.1.0 activate
      neighbor 10.255.1.1 activate
   !
   address-family ipv4
      neighbor 10.1.0.4 activate
      neighbor 10.1.0.10 activate
      network 10.1.0.4/31
      network 10.1.0.10/31
      network 10.1.1.12/32
      network 10.255.1.12/32
   !
   vrf TENANT-A
      rd 10.255.1.12:50000
      route-target import 50000:50000
      route-target export 50000:50000
      redistribute connected
!
end
Leaf3#
```

# Механика

---

# 1. Underlay

* У нас классическая фабрика spine–leaf.
* Поднята IP-маршрутизация (eBGP в твоём случае).
* Все loopback’и (`Lo0` для underlay, `Lo1` для overlay VTEP) друг другу видны.
* Это просто транспорт для туннелей VXLAN.

---

# 2. Overlay (EVPN)

Overlay = **распределённый L2/L3 по BGP EVPN**.

В BGP EVPN есть несколько типов маршрутов:

* **Type-3 (IMET)** — список участников VNI (VTEP-ов). Нужен для BUM (Broadcast, Unknown unicast, Multicast).
* **Type-2 (MAC/IP Advertisement)** — говорит «у меня за этим VTEP есть MAC (и IP, если ARP suppression)».
* **Type-5 (IP Prefix Route)** — нужен для L3VNI: анонс префиксов из VRF.

Когда клиент за Leaf1 говорит «у меня есть MAC/192.168.10.1», Leaf1 анонсирует это в EVPN. Leaf3 получает этот анонс и понимает: чтобы достучаться к 192.168.10.1 → VXLAN-туннель к VTEP Leaf1 (10.255.1.10).

---

# 3. VRF

* Каждый **VRF** = виртуальная маршрутизируемая таблица.
* Мы сделали VRF **TENANT-A**, где у нас есть два префикса:

  * `192.168.10.0/24` (VLAN10 → VNI10100)
  * `192.168.20.0/24` (VLAN20 → VNI10200)
* VRF нужен для **изоляции маршрутов** (каждый «тенант» = свой VRF).
* У VRF есть свой **L3VNI 50000**, через который по EVPN (Type-5) между лифами синхронизируются маршруты.

---

# 4. Anycast Gateway

* На всех лифах один и тот же IP-шлюз (`192.168.10.254` и `192.168.20.254`) и один и тот же виртуальный MAC (`0001.0001.0001`).
* Это значит: клиент в VLAN10 всегда видит «соседа .254» локально на своём Leaf.
* Но фактически этот IP доступен на **каждом Leaf**.
* Благодаря EVPN Type-2 (MAC/IP) и Type-5 (IP Prefix), если пакет нужно отправить в другой VNI или на другой Leaf, он уходит в VXLAN-туннель.

Клиенту не важно, где живёт другой сегмент — Anycast GW есть везде, а лифы «разруливают» через EVPN.

---

# 5. Как идёт трафик

### Сценарий 1: L2 внутри одного VLAN

PC1 (192.168.10.1) → PC2 (192.168.10.2, но за другим Leaf).

1. PC1 делает ARP «кто 192.168.10.2».
2. Leaf1 проверяет EVPN: знает ли IP/MAC? Если да → ARP-suppression (сам отвечает). Если нет → шлёт BUM по VNI10100 (VXLAN multicast через Type-3).
3. Leaf3 получает, отвечает: «192.168.10.2 = MAC xx\:xx\:xx».
4. Теперь Leaf1 знает: MAC/192.168.10.2 → через VTEP 10.255.1.12.
5. VXLAN-туннель установлен, трафик идёт L2 поверх underlay.

---

### Сценарий 2: L3 между VLAN10 и VLAN20

PC1 (192.168.10.1) → PC3 (192.168.20.2).

1. PC1 отправляет пакет на шлюз .254.
2. Leaf1 принимает на SVI Vlan10 (Anycast GW), понимает: пакет в другой VRF-сегмент (192.168.20.0/24).
3. Leaf1 смотрит в VRF TENANT-A и видит по EVPN Type-5, что сеть 192.168.20.0/24 есть за VTEP Leaf3 (10.255.1.12).
4. Leaf1 инкапсулирует пакет в VXLAN с **VNI50000 (L3VNI)** и шлёт его на Leaf3.
5. Leaf3 получает, «выходит» из L3VNI, пересаживает в VNI10200 (VLAN20) и отправляет в порт к PC3.

---

# 6. ARP Suppression

* Когда клиент делает ARP, Leaf локально отвечает, если знает MAC/IP из EVPN Type-2.
* Это экономит broadcast и ускоряет lookup.

---

Шпаргалка
* **Underlay** = транспорт IP (всё видят loopback-и).
* **Overlay** = L2/L3 фабрика на базе BGP EVPN.
* **VRF + L3VNI** = маршрутизация между сегментами через Type-5.
* **Anycast GW** = единый шлюз, но распределённый на каждом Leaf.
* **EVPN RT-2/3/5** = механика синхронизации MAC/IP/Prefix.

---

## Заметки - Что такое IRB

IRB = Integrated Routing and Bridging.
Это модель, где Leaf одновременно:
выполняет bridging (L2) внутри VLAN (VNI),
и routing (L3) между VLAN-ами.

2. Symmetric vs Asymmetric IRB

Asymmetric IRB (раньше часто применялся):
Клиент в VLAN10 шлёт на GW .254 → Leaf1 маршрутизирует сразу в VLAN20 → и шлёт VXLAN уже с VNI10200.
То есть инкапсуляция зависит от исходного сегмента.
Минус: для N сегментов нужно иметь N×N связей (все лифы должны уметь терминировать все L2VNI).
Symmetric IRB (современный подход, Arista/Cisco рекомендуют):
Клиент в VLAN10 шлёт на GW .254 → Leaf1 маршрутизирует в L3VNI (один общий, у нас VNI50000).
Трафик всегда идёт по VXLAN с L3VNI.
На удалённом Leaf3 трафик из L3VNI «высаживается» в VLAN20 (VNI10200).
Минус Asymmetric здесь устранён: маршрутизация симметрична, всегда через один L3VNI, независимо от исходного VLAN.

3. Механика Symmetric IRB

На каждом Leaf поднимаются SVI с Anycast GW (.254).
Все VRF имеют по одному L3VNI (у нас 50000).
Все маршруты сегментов (192.168.10.0/24, 192.168.20.0/24) анонсятся в EVPN как Type-5 префиксы с RT 50000:50000.
При маршрутизации:
Leaf1 принимает пакет в VLAN10.
Делаем L3 lookup в VRF → выбираем путь до 192.168.20.0/24.
Оборачиваем во VXLAN с L3VNI.
Удалённый Leaf3 получает → «понимает» что это L3VNI → высаживает в VLAN20.
