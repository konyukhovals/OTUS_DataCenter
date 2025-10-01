
Hometask 2.15 - VxLAN EVPN L3 + MC-LAG

# Наша топология

Прердыдущую схему не трогал, развернул всё на Leaf1,4, и Border. Тестовые клиенты 172.16.0.1 ext, 172.16.1.1 ext.

Сделал на убогой статике с суммаризацией. костыль на костыле. можно задуматься о ebgp в отдельных врфах. мне ни статика не нравится, ни ebgp, если честно. 
То есть, сейчас мы суммарный маршрут таскаем, да, а в врфах на лифах мы статику пишем. убого выглядит, немасштабируемо работает. лучше уж и правда ebgp, но это слегка усложняет схему и накладывает требования на оборудование  заказчикаю однако, у заказчика сети имеют свойство плодиться, опять придется статикой работать - поэтому лучше всё равно ebgp. да и суммаризация - я не знаю, это сегодня актуально? про оптимизацию таблиц маршрутизации мне бы не хотелось рассматривать аргумент, сегодня это не похоже на аргумент (ну ок, может, я не прав и это кому-то нужно).

<img width="1920" height="1287" alt="image" src="https://github.com/user-attachments/assets/b8c3aa19-9b1d-42a3-97c5-7e647084e1a4" />

# Наша адресация

Адресация лупбеков

| Устройство | Loopback0 (Underlay) |	Loopback1 (Overlay) |
|:-----------|:---------------------|:--------------------|
|Spine 1|10.1.1.0/32|10.255.1.0/32|
|Spine 2|10.1.1.1/32|10.255.1.1/32|
|Spine 3|10.1.1.2/32|10.255.1.2/32|
|Leaf 1|10.1.1.10/32|10.255.1.10/32|
|Leaf 2|10.1.1.11/32|10.255.1.11/32|
|Leaf 3|10.1.1.12/32|10.255.1.12/32|
|Leaf 3|10.1.1.13/32|10.255.1.13/32|

Адресация p2p-подсетей /31, от первого адреса подсети от каждого спайна к лифу

| Устройство | Устройство |	Подсеть |
|:-----------|:---------------------|:--------------------|
|Spine 1|Leaf 1|10.1.0.0/31|
|Spine 1|Leaf 2|10.1.0.2/31|
|Spine 1|Leaf 3|10.1.0.4/31|
|Spine 1|Leaf 4|10.1.0.6/31|
|||
|Spine 2|Leaf 1|10.1.0.8/31|
|Spine 2|Leaf 2|10.1.0.10/31|
|Spine 2|Leaf 3|10.1.0.12/31|
|Spine 2|Leaf 4|10.1.0.14/31|
|||
|Spine 3|Leaf 1|10.1.0.16/31|
|Spine 3|Leaf 2|10.1.0.18/31|
|Spine 3|Leaf 3|10.1.0.20/31|
|Spine 3|Leaf 4|10.1.0.22/31|

# Первичный конфиг Arista

```
admin

en
conf t
zerotouch cancel

enable
configure terminal
ip routing
service routing protocols model multi-agent
end
write memory
reload
```

# Демонстрация работы схемы

Простые пинги по схеме

<img width="927" height="594" alt="image" src="https://github.com/user-attachments/assets/f48cd72f-d28f-4fae-aba7-38b0cecac61f" />

## Команды для разнообразия
```
show bgp evpn route-type ip-prefix ipv4
show ip route vrf CLIENT-BORDER-A
show ip route vrf CLIENT-BORDER-B

show arp vrf CLIENT-BORDER-A
show arp vrf CLIENT-BORDER-B
show mac address-table dynamic
```

```

Leaf1#
Leaf1#show bgp evpn route-type ip-prefix ipv4
BGP routing table information for VRF default
Router identifier 10.1.1.10, local AS number 65210
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexth
op

          Network                Next Hop              Metric  LocPref Weight  Path
 * >      RD: 10.255.1.10:50030 ip-prefix 172.16.0.0/23
                                 -                     -       -       0       i
 * >Ec    RD: 10.255.1.13:50040 ip-prefix 172.16.0.0/23
                                 10.255.1.13           -       100     0       65000 65213 i
 *  ec    RD: 10.255.1.13:50040 ip-prefix 172.16.0.0/23
                                 10.255.1.13           -       100     0       65000 65213 i
 *  ec    RD: 10.255.1.13:50040 ip-prefix 172.16.0.0/23
                                 10.255.1.13           -       100     0       65000 65213 i
 * >      RD: 10.255.1.10:50030 ip-prefix 172.16.10.0/30
                                 -                     -       -       0       i
 * >Ec    RD: 10.255.1.13:50040 ip-prefix 172.16.10.4/30
                                 10.255.1.13           -       100     0       65000 65213 i
 *  ec    RD: 10.255.1.13:50040 ip-prefix 172.16.10.4/30
                                 10.255.1.13           -       100     0       65000 65213 i
 *  ec    RD: 10.255.1.13:50040 ip-prefix 172.16.10.4/30
                                 10.255.1.13           -       100     0       65000 65213 i
 * >      RD: 10.255.1.10:50000 ip-prefix 192.168.10.0/24
                                 -                     -       -       0       i
 * >Ec    RD: 10.255.1.11:50000 ip-prefix 192.168.10.0/24
                                 10.255.1.11           -       100     0       65000 65211 i
 *  ec    RD: 10.255.1.11:50000 ip-prefix 192.168.10.0/24
                                 10.255.1.11           -       100     0       65000 65211 i
 *  ec    RD: 10.255.1.11:50000 ip-prefix 192.168.10.0/24
                                 10.255.1.11           -       100     0       65000 65211 i
 * >Ec    RD: 10.255.1.12:50000 ip-prefix 192.168.10.0/24
                                 10.255.1.12           -       100     0       65000 65212 i
 *  ec    RD: 10.255.1.12:50000 ip-prefix 192.168.10.0/24
                                 10.255.1.12           -       100     0       65000 65212 i
 *  ec    RD: 10.255.1.12:50000 ip-prefix 192.168.10.0/24
                                 10.255.1.12           -       100     0       65000 65212 i
 * >Ec    RD: 10.255.1.13:50000 ip-prefix 192.168.10.0/24
                                 10.255.1.13           -       100     0       65000 65213 i
 *  ec    RD: 10.255.1.13:50000 ip-prefix 192.168.10.0/24
                                 10.255.1.13           -       100     0       65000 65213 i
 *  ec    RD: 10.255.1.13:50000 ip-prefix 192.168.10.0/24
                                 10.255.1.13           -       100     0       65000 65213 i
 * >      RD: 10.255.1.10:50000 ip-prefix 192.168.20.0/24
                                 -                     -       -       0       i
 * >Ec    RD: 10.255.1.11:50000 ip-prefix 192.168.20.0/24
                                 10.255.1.11           -       100     0       65000 65211 i
 *  ec    RD: 10.255.1.11:50000 ip-prefix 192.168.20.0/24
                                 10.255.1.11           -       100     0       65000 65211 i
 *  ec    RD: 10.255.1.11:50000 ip-prefix 192.168.20.0/24
                                 10.255.1.11           -       100     0       65000 65211 i
 * >Ec    RD: 10.255.1.12:50000 ip-prefix 192.168.20.0/24
                                 10.255.1.12           -       100     0       65000 65212 i
 *  ec    RD: 10.255.1.12:50000 ip-prefix 192.168.20.0/24
                                 10.255.1.12           -       100     0       65000 65212 i
 *  ec    RD: 10.255.1.12:50000 ip-prefix 192.168.20.0/24
                                 10.255.1.12           -       100     0       65000 65212 i
 * >Ec    RD: 10.255.1.13:50000 ip-prefix 192.168.20.0/24
                                 10.255.1.13           -       100     0       65000 65213 i
 *  ec    RD: 10.255.1.13:50000 ip-prefix 192.168.20.0/24
                                 10.255.1.13           -       100     0       65000 65213 i
 *  ec    RD: 10.255.1.13:50000 ip-prefix 192.168.20.0/24
                                 10.255.1.13           -       100     0       65000 65213 i
Leaf1#
Leaf1#
Leaf1#
Leaf1#
Leaf1#
Leaf1#show ip route vrf CLIENT-BORDER-A

VRF: CLIENT-BORDER-A
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

Gateway of last resort:
 S        0.0.0.0/0 [1/0] via 172.16.10.2, Ethernet7

 C        172.16.0.0/24 is directly connected, Vlan30
 S        172.16.1.0/24 [1/0] via 172.16.10.2, Ethernet7
 A B      172.16.0.0/23 is directly connected, Null0
 C        172.16.10.0/30 is directly connected, Ethernet7

Leaf1#
Leaf1#show ip route vrf CLIENT-BORDER-B
% IP Routing table for VRF CLIENT-BORDER-B does not exist.
Leaf1#
Leaf1#
Leaf1#
Leaf1#show arp vrf CLIENT-BORDER-A
Address         Age (sec)  Hardware Addr   Interface
172.16.10.2       1:20:13  5055.39c6.2777  Ethernet7
172.16.0.1        1:15:20  0050.7966.680d  Vlan30, Ethernet6
Leaf1#
Leaf1#show arp vrf CLIENT-BORDER-B
% ARP table for VRF CLIENT-BORDER-B does not exist.
Leaf1#
Leaf1#show mac address-table dynamic
          Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports      Moves   Last Move
----    -----------       ----        -----      -----   ---------
  30    0050.7966.680d    DYNAMIC     Et6        1       0:07:57 ago
4090    5023.637b.8a38    DYNAMIC     Vx1        1       1:13:45 ago
Total Mac Addresses for this criterion: 2

          Multicast Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       ----        -----
Total Mac Addresses for this criterion: 0
Leaf1#
Leaf1#



Leaf4#
Leaf4#
Leaf4#show bgp evpn route-type ip-prefix ipv4
BGP routing table information for VRF default
Router identifier 10.1.1.13, local AS number 65213
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexth
op

          Network                Next Hop              Metric  LocPref Weight  Path
 * >Ec    RD: 10.255.1.10:50030 ip-prefix 172.16.0.0/23
                                 10.255.1.10           -       100     0       65000 65210 i
 *  ec    RD: 10.255.1.10:50030 ip-prefix 172.16.0.0/23
                                 10.255.1.10           -       100     0       65000 65210 i
 *  ec    RD: 10.255.1.10:50030 ip-prefix 172.16.0.0/23
                                 10.255.1.10           -       100     0       65000 65210 i
 * >      RD: 10.255.1.13:50040 ip-prefix 172.16.0.0/23
                                 -                     -       -       0       i
 * >Ec    RD: 10.255.1.10:50030 ip-prefix 172.16.10.0/30
                                 10.255.1.10           -       100     0       65000 65210 i
 *  ec    RD: 10.255.1.10:50030 ip-prefix 172.16.10.0/30
                                 10.255.1.10           -       100     0       65000 65210 i
 *  ec    RD: 10.255.1.10:50030 ip-prefix 172.16.10.0/30
                                 10.255.1.10           -       100     0       65000 65210 i
 * >      RD: 10.255.1.13:50040 ip-prefix 172.16.10.4/30
                                 -                     -       -       0       i
 * >Ec    RD: 10.255.1.10:50000 ip-prefix 192.168.10.0/24
                                 10.255.1.10           -       100     0       65000 65210 i
 *  ec    RD: 10.255.1.10:50000 ip-prefix 192.168.10.0/24
                                 10.255.1.10           -       100     0       65000 65210 i
 *  ec    RD: 10.255.1.10:50000 ip-prefix 192.168.10.0/24
                                 10.255.1.10           -       100     0       65000 65210 i
 * >Ec    RD: 10.255.1.11:50000 ip-prefix 192.168.10.0/24
                                 10.255.1.11           -       100     0       65000 65211 i
 *  ec    RD: 10.255.1.11:50000 ip-prefix 192.168.10.0/24
                                 10.255.1.11           -       100     0       65000 65211 i
 *  ec    RD: 10.255.1.11:50000 ip-prefix 192.168.10.0/24
                                 10.255.1.11           -       100     0       65000 65211 i
 * >Ec    RD: 10.255.1.12:50000 ip-prefix 192.168.10.0/24
                                 10.255.1.12           -       100     0       65000 65212 i
 *  ec    RD: 10.255.1.12:50000 ip-prefix 192.168.10.0/24
                                 10.255.1.12           -       100     0       65000 65212 i
 *  ec    RD: 10.255.1.12:50000 ip-prefix 192.168.10.0/24
                                 10.255.1.12           -       100     0       65000 65212 i
 * >      RD: 10.255.1.13:50000 ip-prefix 192.168.10.0/24
                                 -                     -       -       0       i
 * >Ec    RD: 10.255.1.10:50000 ip-prefix 192.168.20.0/24
                                 10.255.1.10           -       100     0       65000 65210 i
 *  ec    RD: 10.255.1.10:50000 ip-prefix 192.168.20.0/24
                                 10.255.1.10           -       100     0       65000 65210 i
 *  ec    RD: 10.255.1.10:50000 ip-prefix 192.168.20.0/24
                                 10.255.1.10           -       100     0       65000 65210 i
 * >Ec    RD: 10.255.1.11:50000 ip-prefix 192.168.20.0/24
                                 10.255.1.11           -       100     0       65000 65211 i
 *  ec    RD: 10.255.1.11:50000 ip-prefix 192.168.20.0/24
                                 10.255.1.11           -       100     0       65000 65211 i
 *  ec    RD: 10.255.1.11:50000 ip-prefix 192.168.20.0/24
                                 10.255.1.11           -       100     0       65000 65211 i
 * >Ec    RD: 10.255.1.12:50000 ip-prefix 192.168.20.0/24
                                 10.255.1.12           -       100     0       65000 65212 i
 *  ec    RD: 10.255.1.12:50000 ip-prefix 192.168.20.0/24
                                 10.255.1.12           -       100     0       65000 65212 i
 *  ec    RD: 10.255.1.12:50000 ip-prefix 192.168.20.0/24
                                 10.255.1.12           -       100     0       65000 65212 i
 * >      RD: 10.255.1.13:50000 ip-prefix 192.168.20.0/24
                                 -                     -       -       0       i
Leaf4#
Leaf4#
Leaf4#
Leaf4#
Leaf4#
Leaf4#show ip route vrf CLIENT-BORDER-A
% IP Routing table for VRF CLIENT-BORDER-A does not exist.
Leaf4#
Leaf4#show ip route vrf CLIENT-BORDER-B

VRF: CLIENT-BORDER-B
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

Gateway of last resort:
 S        0.0.0.0/0 [1/0] via 172.16.10.6, Ethernet7

 S        172.16.0.0/24 [1/0] via 172.16.10.6, Ethernet7
 C        172.16.1.0/24 is directly connected, Vlan40
 A B      172.16.0.0/23 is directly connected, Null0
 C        172.16.10.4/30 is directly connected, Ethernet7

Leaf4#
Leaf4#
Leaf4#
Leaf4#show arp vrf CLIENT-BORDER-A
% ARP table for VRF CLIENT-BORDER-A does not exist.
Leaf4#
Leaf4#show arp vrf CLIENT-BORDER-B
Address         Age (sec)  Hardware Addr   Interface
172.16.10.6       1:21:42  5055.39c6.2777  Ethernet7
172.16.1.1        1:15:42  0050.7966.6815  Vlan40, Ethernet6
Leaf4#
Leaf4#show mac address-table dynamic
          Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports      Moves   Last Move
----    -----------       ----        -----      -----   ---------
  40    0050.7966.6815    DYNAMIC     Et6        1       0:00:06 ago
4089    50ab.2cfe.57e5    DYNAMIC     Vx1        1       1:26:22 ago
Total Mac Addresses for this criterion: 2

          Multicast Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       ----        -----
Total Mac Addresses for this criterion: 0
Leaf4#
Leaf4#
Leaf4#
```


## Spine1

```
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
   no switchport
   ip address 10.1.0.6/31
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
   no bgp default ipv4-unicast
   maximum-paths 4
   neighbor EVPN-OVERLAY peer group
   neighbor EVPN-OVERLAY next-hop-unchanged
   neighbor EVPN-OVERLAY update-source Loopback1
   neighbor EVPN-OVERLAY ebgp-multihop 3
   neighbor EVPN-OVERLAY send-community extended
   neighbor UNDERLAY peer group
   neighbor UNDERLAY send-community
   neighbor 10.1.0.1 peer group UNDERLAY
   neighbor 10.1.0.1 remote-as 65210
   neighbor 10.1.0.3 peer group UNDERLAY
   neighbor 10.1.0.3 remote-as 65211
   neighbor 10.1.0.5 peer group UNDERLAY
   neighbor 10.1.0.5 remote-as 65212
   neighbor 10.1.0.7 peer group UNDERLAY
   neighbor 10.1.0.7 remote-as 65213
   neighbor 10.255.1.10 peer group EVPN-OVERLAY
   neighbor 10.255.1.10 remote-as 65210
   neighbor 10.255.1.11 peer group EVPN-OVERLAY
   neighbor 10.255.1.11 remote-as 65211
   neighbor 10.255.1.12 peer group EVPN-OVERLAY
   neighbor 10.255.1.12 remote-as 65212
   neighbor 10.255.1.13 peer group EVPN-OVERLAY
   neighbor 10.255.1.13 remote-as 65213
   !
   address-family evpn
      neighbor EVPN-OVERLAY activate
      neighbor 10.255.1.10 activate
      neighbor 10.255.1.11 activate
      neighbor 10.255.1.12 activate
      neighbor 10.255.1.13 activate
   !
   address-family ipv4
      neighbor UNDERLAY activate
      network 10.1.0.0/31
      network 10.1.0.2/31
      network 10.1.0.4/31
      network 10.1.0.6/31
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
   ip address 10.1.0.8/31
!
interface Ethernet2
   no switchport
   ip address 10.1.0.10/31
!
interface Ethernet3
   no switchport
   ip address 10.1.0.12/31
!
interface Ethernet4
   no switchport
   ip address 10.1.0.14/31
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
   no bgp default ipv4-unicast
   maximum-paths 4
   neighbor EVPN-OVERLAY peer group
   neighbor EVPN-OVERLAY next-hop-unchanged
   neighbor EVPN-OVERLAY update-source Loopback1
   neighbor EVPN-OVERLAY ebgp-multihop 3
   neighbor EVPN-OVERLAY send-community extended
   neighbor UNDERLAY peer group
   neighbor UNDERLAY send-community
   neighbor 10.1.0.9 peer group UNDERLAY
   neighbor 10.1.0.9 remote-as 65210
   neighbor 10.1.0.11 peer group UNDERLAY
   neighbor 10.1.0.11 remote-as 65211
   neighbor 10.1.0.13 peer group UNDERLAY
   neighbor 10.1.0.13 remote-as 65212
   neighbor 10.1.0.15 peer group UNDERLAY
   neighbor 10.1.0.15 remote-as 65213
   neighbor 10.255.1.10 peer group EVPN-OVERLAY
   neighbor 10.255.1.10 remote-as 65210
   neighbor 10.255.1.11 peer group EVPN-OVERLAY
   neighbor 10.255.1.11 remote-as 65211
   neighbor 10.255.1.12 peer group EVPN-OVERLAY
   neighbor 10.255.1.12 remote-as 65212
   neighbor 10.255.1.13 peer group EVPN-OVERLAY
   neighbor 10.255.1.13 remote-as 65213
   !
   address-family evpn
      neighbor EVPN-OVERLAY activate
      neighbor 10.255.1.10 activate
      neighbor 10.255.1.11 activate
      neighbor 10.255.1.12 activate
      neighbor 10.255.1.13 activate
   !
   address-family ipv4
      neighbor UNDERLAY activate
      network 10.1.0.8/31
      network 10.1.0.10/31
      network 10.1.0.12/31
      network 10.1.0.14/31
      network 10.1.1.1/32
      network 10.255.1.1/32
!
end
Spine2#
```

## Spine3

```
Spine3#sh run
! Command: show running-config
! device: Spine3 (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Spine3
!
spanning-tree mode mstp
!
interface Ethernet1
   no switchport
   ip address 10.1.0.16/31
!
interface Ethernet2
   no switchport
   ip address 10.1.0.18/31
!
interface Ethernet3
   no switchport
   ip address 10.1.0.20/31
!
interface Ethernet4
   no switchport
   ip address 10.1.0.22/31
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
   ip address 10.1.1.2/32
!
interface Loopback1
   ip address 10.255.1.2/32
!
interface Management1
!
ip routing
!
router bgp 65000
   router-id 10.1.1.2
   no bgp default ipv4-unicast
   maximum-paths 4
   neighbor EVPN-OVERLAY peer group
   neighbor EVPN-OVERLAY next-hop-unchanged
   neighbor EVPN-OVERLAY update-source Loopback1
   neighbor EVPN-OVERLAY ebgp-multihop 3
   neighbor EVPN-OVERLAY send-community extended
   neighbor UNDERLAY peer group
   neighbor UNDERLAY send-community
   neighbor 10.1.0.17 peer group UNDERLAY
   neighbor 10.1.0.17 remote-as 65210
   neighbor 10.1.0.19 peer group UNDERLAY
   neighbor 10.1.0.19 remote-as 65211
   neighbor 10.1.0.21 peer group UNDERLAY
   neighbor 10.1.0.21 remote-as 65212
   neighbor 10.1.0.23 peer group UNDERLAY
   neighbor 10.1.0.23 remote-as 65213
   neighbor 10.255.1.10 peer group EVPN-OVERLAY
   neighbor 10.255.1.10 remote-as 65210
   neighbor 10.255.1.11 peer group EVPN-OVERLAY
   neighbor 10.255.1.11 remote-as 65211
   neighbor 10.255.1.12 peer group EVPN-OVERLAY
   neighbor 10.255.1.12 remote-as 65212
   neighbor 10.255.1.13 peer group EVPN-OVERLAY
   neighbor 10.255.1.13 remote-as 65213
   !
   address-family evpn
      neighbor EVPN-OVERLAY activate
      neighbor 10.255.1.10 activate
      neighbor 10.255.1.11 activate
      neighbor 10.255.1.12 activate
      neighbor 10.255.1.13 activate
   !
   address-family ipv4
      neighbor UNDERLAY activate
      network 10.1.0.16/31
      network 10.1.0.18/31
      network 10.1.0.20/31
      network 10.1.0.22/31
      network 10.1.1.2/32
      network 10.255.1.2/32
!
end
Spine3#
```

## Leaf1

```

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
vlan 10
   name TEN
!
vlan 20
   name TWENTY
!
vlan 30
   name CLIENT-BORDER-A
!
vrf instance CLIENT-BORDER-A
!
vrf instance TENANT
!
interface Ethernet1
   no switchport
   ip address 10.1.0.1/31
!
interface Ethernet2
   no switchport
   ip address 10.1.0.9/31
!
interface Ethernet3
   no switchport
   ip address 10.1.0.17/31
!
interface Ethernet4
!
interface Ethernet5
   switchport access vlan 10
!
interface Ethernet6
   switchport access vlan 30
   spanning-tree portfast
!
interface Ethernet7
   description To-External-Router (CLIENT-BORDER-A)
   no switchport
   vrf CLIENT-BORDER-A
   ip address 172.16.10.1/30
!
interface Ethernet8
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
   vrf TENANT
   ip address virtual 192.168.10.254/24
!
interface Vlan20
   no autostate
   vrf TENANT
   ip address virtual 192.168.20.254/24
!
interface Vlan30
   no autostate
   vrf CLIENT-BORDER-A
   ip address virtual 172.16.0.254/24
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 1010
   vxlan vlan 20 vni 1020
   vxlan vrf CLIENT-BORDER-A vni 50030
   vxlan vrf TENANT vni 50000
!
ip virtual-router mac-address 00:01:00:01:00:01
!
ip routing
ip routing vrf CLIENT-BORDER-A
ip routing vrf TENANT
!
ip route vrf CLIENT-BORDER-A 0.0.0.0/0 172.16.10.2
ip route vrf CLIENT-BORDER-A 172.16.1.0/24 172.16.10.2
ip route vrf TENANT 192.168.10.0/24 Null0
ip route vrf TENANT 192.168.20.0/24 Null0
!
router bgp 65210
   router-id 10.1.1.10
   no bgp default ipv4-unicast
   rd auto
   maximum-paths 4
   neighbor EVPN-OVERLAY peer group
   neighbor EVPN-OVERLAY next-hop-unchanged
   neighbor EVPN-OVERLAY update-source Loopback1
   neighbor EVPN-OVERLAY ebgp-multihop 3
   neighbor EVPN-OVERLAY send-community extended
   neighbor UNDERLAY peer group
   neighbor UNDERLAY send-community
   neighbor 10.1.0.0 peer group UNDERLAY
   neighbor 10.1.0.0 remote-as 65000
   neighbor 10.1.0.8 peer group UNDERLAY
   neighbor 10.1.0.8 remote-as 65000
   neighbor 10.1.0.16 peer group UNDERLAY
   neighbor 10.1.0.16 remote-as 65000
   neighbor 10.255.1.0 peer group EVPN-OVERLAY
   neighbor 10.255.1.0 remote-as 65000
   neighbor 10.255.1.1 peer group EVPN-OVERLAY
   neighbor 10.255.1.1 remote-as 65000
   neighbor 10.255.1.2 peer group EVPN-OVERLAY
   neighbor 10.255.1.2 remote-as 65000
   redistribute connected
   !
   vlan 10
      rd auto
      route-target both 1010:1010
      redistribute learned
   !
   vlan 20
      rd auto
      route-target both 1020:1020
      redistribute learned
   !
   address-family evpn
      neighbor 10.255.1.0 activate
      neighbor 10.255.1.1 activate
      neighbor 10.255.1.2 activate
   !
   address-family ipv4
      neighbor UNDERLAY activate
      network 10.1.0.0/31
      network 10.1.0.8/31
      network 10.1.0.16/31
      network 10.1.1.10/32
      network 10.255.1.10/32
   !
   vrf CLIENT-BORDER-A
      rd 10.255.1.10:50030
      route-target import evpn 50030:50030
      route-target export evpn 50030:50030
      network 172.16.0.0/24
      aggregate-address 172.16.0.0/23 summary-only
      redistribute connected
   !
   vrf TENANT
      rd 10.255.1.10:50000
      route-target import evpn 50000:50000
      route-target export evpn 50000:50000
      redistribute connected
!
end
Leaf1#
```

## Leaf2

```
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
vlan 10
   name TEN
!
vlan 20
   name TWENTY
!
vlan 4093
   name MLAG-KEEPALIVE
!
vlan 4094
   name MLAG-PEER
   trunk group MLAG-PEER
!
vrf instance TENANT
!
interface Port-Channel10
   description CLIENT_LACP_Right
   switchport access vlan 10
   mlag 10
   spanning-tree portfast
!
interface Port-Channel200
   description MLAG PEERLINK to Leaf1
   switchport mode trunk
   switchport trunk group MLAG-PEER
!
interface Ethernet1
   no switchport
   ip address 10.1.0.3/31
!
interface Ethernet2
   no switchport
   ip address 10.1.0.11/31
!
interface Ethernet3
   no switchport
   ip address 10.1.0.19/31
!
interface Ethernet4
!
interface Ethernet5
   channel-group 10 mode active
!
interface Ethernet6
   description MALG keepalive to Leaf1
   switchport access vlan 4093
   spanning-tree portfast
!
interface Ethernet7
   description MLAG peer-link to Leaf1
   channel-group 200 mode active
!
interface Ethernet8
   description MLAG peer-link to Leaf1
   channel-group 200 mode active
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
   vrf TENANT
   ip address virtual 192.168.10.254/24
!
interface Vlan20
   no autostate
   vrf TENANT
   ip address virtual 192.168.20.254/24
!
interface Vlan4093
   description MLAG keepalive SVI
   ip address 169.254.12.2/30
!
interface Vlan4094
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 1010
   vxlan vlan 20 vni 1020
   vxlan vrf TENANT vni 50000
!
ip virtual-router mac-address 00:01:00:01:00:01
!
ip routing
ip routing vrf TENANT
!
mlag configuration
   domain-id MLAG12
   local-interface Vlan4093
   peer-address 169.254.12.1
   peer-link Port-Channel200
   reload-delay mlag 300
   reload-delay non-mlag 330
!
ip route vrf TENANT 192.168.10.0/24 Null0
ip route vrf TENANT 192.168.20.0/24 Null0
!
router bgp 65211
   router-id 10.1.1.11
   no bgp default ipv4-unicast
   rd auto
   maximum-paths 4
   neighbor EVPN-OVERLAY peer group
   neighbor EVPN-OVERLAY next-hop-unchanged
   neighbor EVPN-OVERLAY update-source Loopback1
   neighbor EVPN-OVERLAY ebgp-multihop 3
   neighbor EVPN-OVERLAY send-community extended
   neighbor UNDERLAY peer group
   neighbor UNDERLAY send-community
   neighbor 10.1.0.2 peer group UNDERLAY
   neighbor 10.1.0.2 remote-as 65000
   neighbor 10.1.0.10 peer group UNDERLAY
   neighbor 10.1.0.10 remote-as 65000
   neighbor 10.1.0.18 peer group UNDERLAY
   neighbor 10.1.0.18 remote-as 65000
   neighbor 10.255.1.0 peer group EVPN-OVERLAY
   neighbor 10.255.1.0 remote-as 65000
   neighbor 10.255.1.1 peer group EVPN-OVERLAY
   neighbor 10.255.1.1 remote-as 65000
   neighbor 10.255.1.2 peer group EVPN-OVERLAY
   neighbor 10.255.1.2 remote-as 65000
   redistribute connected
   !
   vlan 10
      rd auto
      route-target both 1010:1010
      redistribute learned
   !
   vlan 20
      rd auto
      route-target both 1020:1020
      redistribute learned
   !
   address-family evpn
      neighbor 10.255.1.0 activate
      neighbor 10.255.1.1 activate
      neighbor 10.255.1.2 activate
   !
   address-family ipv4
      neighbor UNDERLAY activate
      network 10.0.0.2/31
      network 10.0.0.10/31
      network 10.0.0.18/31
      network 10.1.1.11/32
      network 10.255.1.11/32
   !
   vrf TENANT
      rd 10.255.1.11:50000
      route-target import evpn 50000:50000
      route-target export evpn 50000:50000
      redistribute connected
!
end
Leaf2#
```

## Leaf3

```
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
vlan 10
   name TEN
   trunk group MLAG-PEER
!
vlan 20
   name TWENTY
   trunk group MLAG-PEER
!
vlan 4093
   name MLAG-KEEPALIVE
!
vlan 4094
   name MLAG-PEER
   trunk group MLAG-PEER
!
vrf instance TENANT
!
interface Port-Channel10
   switchport access vlan 20
   mlag 20
!
interface Port-Channel200
   description MLAG PEER-LINK to Leaf4
   switchport mode trunk
   switchport trunk group MLAG-PEER
!
interface Ethernet1
   no switchport
   ip address 10.1.0.5/31
!
interface Ethernet2
   no switchport
   ip address 10.1.0.13/31
!
interface Ethernet3
   no switchport
   ip address 10.1.0.21/31
!
interface Ethernet4
!
interface Ethernet5
   channel-group 10 mode active
!
interface Ethernet6
   description MLAG keepalive to Leaf4
   switchport access vlan 4093
   spanning-tree portfast
!
interface Ethernet7
   description MLAG peer-lik to Leaf4
   channel-group 200 mode active
!
interface Ethernet8
   description MLAG peer-lik to Leaf4
   channel-group 200 mode active
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
   vrf TENANT
   ip address virtual 192.168.10.254/24
!
interface Vlan20
   no autostate
   vrf TENANT
   ip address virtual 192.168.20.254/24
!
interface Vlan4093
   description MLAG keepalive SVI
   ip address 169.254.12.5/30
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 1010
   vxlan vlan 20 vni 1020
   vxlan vrf TENANT vni 50000
!
ip virtual-router mac-address 00:01:00:01:00:01
!
ip routing
ip routing vrf TENANT
!
mlag configuration
   domain-id MLAG34
   local-interface Vlan4093
   peer-address 169.254.12.6
   peer-link Port-Channel200
   reload-delay mlag 300
   reload-delay non-mlag 330
!
ip route vrf TENANT 192.168.10.0/24 Null0
ip route vrf TENANT 192.168.20.0/24 Null0
!
router bgp 65212
   router-id 10.1.1.12
   no bgp default ipv4-unicast
   rd auto
   maximum-paths 4
   neighbor EVPN-OVERLAY peer group
   neighbor EVPN-OVERLAY next-hop-unchanged
   neighbor EVPN-OVERLAY update-source Loopback1
   neighbor EVPN-OVERLAY ebgp-multihop 3
   neighbor EVPN-OVERLAY send-community extended
   neighbor UNDERLAY peer group
   neighbor UNDERLAY send-community
   neighbor 10.1.0.4 peer group UNDERLAY
   neighbor 10.1.0.4 remote-as 65000
   neighbor 10.1.0.12 peer group UNDERLAY
   neighbor 10.1.0.12 remote-as 65000
   neighbor 10.1.0.20 peer group UNDERLAY
   neighbor 10.1.0.20 remote-as 65000
   neighbor 10.255.1.0 peer group EVPN-OVERLAY
   neighbor 10.255.1.0 remote-as 65000
   neighbor 10.255.1.1 peer group EVPN-OVERLAY
   neighbor 10.255.1.1 remote-as 65000
   neighbor 10.255.1.2 peer group EVPN-OVERLAY
   neighbor 10.255.1.2 remote-as 65000
   redistribute connected
   !
   vlan 10
      rd auto
      route-target both 1010:1010
      redistribute learned
   !
   vlan 20
      rd auto
      route-target both 1020:1020
      redistribute learned
   !
   address-family evpn
      neighbor 10.255.1.0 activate
      neighbor 10.255.1.1 activate
      neighbor 10.255.1.2 activate
   !
   address-family ipv4
      neighbor UNDERLAY activate
      network 10.1.0.4/31
      network 10.1.0.12/31
      network 10.1.0.20/31
      network 10.1.1.12/32
      network 10.255.1.12/32
   !
   vrf TENANT
      rd 10.255.1.12:50000
      route-target import evpn 50000:50000
      route-target export evpn 50000:50000
      redistribute connected
!
end
Leaf3#
```

## Leaf4

```

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
vlan 10
   name TEN
!
vlan 20
   name TWENTY
!
vlan 30
   name CLIENT-BORDER-A
!
vrf instance CLIENT-BORDER-A
!
vrf instance TENANT
!
interface Ethernet1
   no switchport
   ip address 10.1.0.1/31
!
interface Ethernet2
   no switchport
   ip address 10.1.0.9/31
!
interface Ethernet3
   no switchport
   ip address 10.1.0.17/31
!
interface Ethernet4
!
interface Ethernet5
   switchport access vlan 10
!
interface Ethernet6
   switchport access vlan 30
   spanning-tree portfast
!
interface Ethernet7
   description To-External-Router (CLIENT-BORDER-A)
   no switchport
   vrf CLIENT-BORDER-A
   ip address 172.16.10.1/30
!
interface Ethernet8
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
   vrf TENANT
   ip address virtual 192.168.10.254/24
!
interface Vlan20
   no autostate
   vrf TENANT
   ip address virtual 192.168.20.254/24
!
interface Vlan30
   no autostate
   vrf CLIENT-BORDER-A
   ip address virtual 172.16.0.254/24
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 1010
   vxlan vlan 20 vni 1020
   vxlan vrf CLIENT-BORDER-A vni 50030
   vxlan vrf TENANT vni 50000
!
ip virtual-router mac-address 00:01:00:01:00:01
!
ip routing
ip routing vrf CLIENT-BORDER-A
ip routing vrf TENANT
!
ip route vrf CLIENT-BORDER-A 0.0.0.0/0 172.16.10.2
ip route vrf CLIENT-BORDER-A 172.16.1.0/24 172.16.10.2
ip route vrf TENANT 192.168.10.0/24 Null0
ip route vrf TENANT 192.168.20.0/24 Null0
!
router bgp 65210
   router-id 10.1.1.10
   no bgp default ipv4-unicast
   rd auto
   maximum-paths 4
   neighbor EVPN-OVERLAY peer group
   neighbor EVPN-OVERLAY next-hop-unchanged
   neighbor EVPN-OVERLAY update-source Loopback1
   neighbor EVPN-OVERLAY ebgp-multihop 3
   neighbor EVPN-OVERLAY send-community extended
   neighbor UNDERLAY peer group
   neighbor UNDERLAY send-community
   neighbor 10.1.0.0 peer group UNDERLAY
   neighbor 10.1.0.0 remote-as 65000
   neighbor 10.1.0.8 peer group UNDERLAY
   neighbor 10.1.0.8 remote-as 65000
   neighbor 10.1.0.16 peer group UNDERLAY
   neighbor 10.1.0.16 remote-as 65000
   neighbor 10.255.1.0 peer group EVPN-OVERLAY
   neighbor 10.255.1.0 remote-as 65000
   neighbor 10.255.1.1 peer group EVPN-OVERLAY
   neighbor 10.255.1.1 remote-as 65000
   neighbor 10.255.1.2 peer group EVPN-OVERLAY
   neighbor 10.255.1.2 remote-as 65000
   redistribute connected
   !
   vlan 10
      rd auto
      route-target both 1010:1010
      redistribute learned
   !
   vlan 20
      rd auto
      route-target both 1020:1020
      redistribute learned
   !
   address-family evpn
      neighbor 10.255.1.0 activate
      neighbor 10.255.1.1 activate
      neighbor 10.255.1.2 activate
   !
   address-family ipv4
      neighbor UNDERLAY activate
      network 10.1.0.0/31
      network 10.1.0.8/31
      network 10.1.0.16/31
      network 10.1.1.10/32
      network 10.255.1.10/32
   !
   vrf CLIENT-BORDER-A
      rd 10.255.1.10:50030
      route-target import evpn 50030:50030
      route-target export evpn 50030:50030
      network 172.16.0.0/24
      aggregate-address 172.16.0.0/23 summary-only
      redistribute connected
   !
   vrf TENANT
      rd 10.255.1.10:50000
      route-target import evpn 50000:50000
      route-target export evpn 50000:50000
      redistribute connected
!
end
Leaf1#
```

## Border router from crazy customer
```
rder#
Border#sh run
! Command: show running-config
! device: Border (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model ribd
!
hostname Border
!
spanning-tree mode mstp
!
interface Ethernet1
   description To-Leaf1 (CLIENT-A)
   no switchport
   ip address 172.16.10.2/30
!
interface Ethernet2
   description To-Leaf4 (CLIENT-B)
   no switchport
   ip address 172.16.10.6/30
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
interface Management1
!
ip routing
!
ip route 172.16.0.0/24 172.16.10.1
ip route 172.16.1.0/24 172.16.10.5
!
end
Border#sh ip route

VRF: default
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

 S        172.16.0.0/24 [1/0] via 172.16.10.1, Ethernet1
 S        172.16.1.0/24 [1/0] via 172.16.10.5, Ethernet2
 C        172.16.10.0/30 is directly connected, Ethernet1
 C        172.16.10.4/30 is directly connected, Ethernet2

Border#sh arp
Address         Age (sec)  Hardware Addr   Interface
172.16.10.1       1:10:10  50ab.2cfe.57e5  Ethernet1
172.16.10.5       1:10:09  5023.637b.8a38  Ethernet2
Border#

```


