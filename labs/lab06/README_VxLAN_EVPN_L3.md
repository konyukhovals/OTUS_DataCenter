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
Spine1#
Spine1#show ip bgp summary
BGP summary information for VRF default
Router identifier 10.1.1.0, local AS number 65000
Neighbor Status Codes: m - Under maintenance
  Neighbor    V AS           MsgRcvd   MsgSent  InQ OutQ  Up/Down State   PfxRcd PfxAcc
  10.1.0.1    4 65210             29        28    0    0 00:16:44 Estab   4      4
  10.1.0.3    4 65211             26        28    0    0 00:16:31 Estab   6      6
  10.1.0.5    4 65212             25        27    0    0 00:16:17 Estab   6      6
  10.255.1.10 4 65210             50        40    0    0 00:16:43 Estab   4      4
  10.255.1.11 4 65211             42        47    0    0 00:16:30 Estab   6      6
  10.255.1.12 4 65212             42        40    0    0 00:16:16 Estab   6      6
Spine1#
Spine1#show bgp evpn summary
BGP summary information for VRF default
Router identifier 10.1.1.0, local AS number 65000
Neighbor Status Codes: m - Under maintenance
  Neighbor    V AS           MsgRcvd   MsgSent  InQ OutQ  Up/Down State   PfxRcd PfxAcc
  10.255.1.10 4 65210             50        40    0    0 00:16:43 Estab   6      6
  10.255.1.11 4 65211             42        47    0    0 00:16:30 Estab   2      2
  10.255.1.12 4 65212             42        40    0    0 00:16:16 Estab   6      6
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
 * >      RD: 10.1.1.12:10 mac-ip 0050.7966.6802 192.168.10.2
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
Spine1#show bgp evpn route-type ip-prefix ipv4
BGP routing table information for VRF default
Router identifier 10.1.1.0, local AS number 65000
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
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
```

```

Leaf1#
Leaf1#
Leaf1#
Leaf1#
Leaf1#
Leaf1#
Leaf1#show ip bgp summary
BGP summary information for VRF default
Router identifier 10.1.1.10, local AS number 65210
Neighbor Status Codes: m - Under maintenance
  Neighbor   V AS           MsgRcvd   MsgSent  InQ OutQ  Up/Down State   PfxRcd PfxAcc
  10.1.0.0   4 65000             32        33    0    0 00:20:00 Estab   13     13
  10.1.0.6   4 65000             32        37    0    0 00:20:00 Estab   13     13
  10.255.1.0 4 65000             44        53    0    0 00:19:59 Estab   13     13
  10.255.1.1 4 65000             44        54    0    0 00:19:59 Estab   13     13
Leaf1#
Leaf1#show bgp evpn summary
BGP summary information for VRF default
Router identifier 10.1.1.10, local AS number 65210
Neighbor Status Codes: m - Under maintenance
  Neighbor   V AS           MsgRcvd   MsgSent  InQ OutQ  Up/Down State   PfxRcd PfxAcc
  10.255.1.0 4 65000             44        53    0    0 00:19:59 Estab   8      8
  10.255.1.1 4 65000             44        54    0    0 00:19:59 Estab   8      8
Leaf1#
Leaf1#show bgp evpn route-type imet
BGP routing table information for VRF default
Router identifier 10.1.1.10, local AS number 65210
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
 * >      RD: 10.1.1.10:10 imet 10.255.1.10
                                 -                     -       -       0       i
 * >      RD: 10.1.1.10:20 imet 10.255.1.10
                                 -                     -       -       0       i
 * >Ec    RD: 10.1.1.11:10 imet 10.255.1.11
                                 10.255.1.11           -       100     0       65000 65211 i
 *  ec    RD: 10.1.1.11:10 imet 10.255.1.11
                                 10.255.1.11           -       100     0       65000 65211 i
 * >Ec    RD: 10.1.1.11:20 imet 10.255.1.11
                                 10.255.1.11           -       100     0       65000 65211 i
 *  ec    RD: 10.1.1.11:20 imet 10.255.1.11
                                 10.255.1.11           -       100     0       65000 65211 i
 * >Ec    RD: 10.1.1.12:10 imet 10.255.1.12
                                 10.255.1.12           -       100     0       65000 65212 i
 *  ec    RD: 10.1.1.12:10 imet 10.255.1.12
                                 10.255.1.12           -       100     0       65000 65212 i
 * >Ec    RD: 10.1.1.12:20 imet 10.255.1.12
                                 10.255.1.12           -       100     0       65000 65212 i
 *  ec    RD: 10.1.1.12:20 imet 10.255.1.12
                                 10.255.1.12           -       100     0       65000 65212 i
Leaf1#
Leaf1#show bgp evpn route-type mac-ip
BGP routing table information for VRF default
Router identifier 10.1.1.10, local AS number 65210
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
 * >      RD: 10.1.1.10:10 mac-ip 0050.7966.6801
                                 -                     -       -       0       i
 * >      RD: 10.1.1.10:10 mac-ip 0050.7966.6801 192.168.10.1
                                 -                     -       -       0       i
 * >Ec    RD: 10.1.1.12:10 mac-ip 0050.7966.6802
                                 10.255.1.12           -       100     0       65000 65212 i
 *  ec    RD: 10.1.1.12:10 mac-ip 0050.7966.6802
                                 10.255.1.12           -       100     0       65000 65212 i
 * >Ec    RD: 10.1.1.12:10 mac-ip 0050.7966.6802 192.168.10.2
                                 10.255.1.12           -       100     0       65000 65212 i
 *  ec    RD: 10.1.1.12:10 mac-ip 0050.7966.6802 192.168.10.2
                                 10.255.1.12           -       100     0       65000 65212 i
 * >      RD: 10.1.1.10:20 mac-ip 0050.7966.6803
                                 -                     -       -       0       i
 * >      RD: 10.1.1.10:20 mac-ip 0050.7966.6803 192.168.20.1
                                 -                     -       -       0       i
 * >Ec    RD: 10.1.1.12:20 mac-ip 0050.7966.6804
                                 10.255.1.12           -       100     0       65000 65212 i
 *  ec    RD: 10.1.1.12:20 mac-ip 0050.7966.6804
                                 10.255.1.12           -       100     0       65000 65212 i
 * >Ec    RD: 10.1.1.12:20 mac-ip 0050.7966.6804 192.168.20.2
                                 10.255.1.12           -       100     0       65000 65212 i
 *  ec    RD: 10.1.1.12:20 mac-ip 0050.7966.6804 192.168.20.2
                                 10.255.1.12           -       100     0       65000 65212 i
Leaf1#
Leaf1#show bgp evpn route-type ip-prefix ipv4
BGP routing table information for VRF default
Router identifier 10.1.1.10, local AS number 65210
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
Leaf1#
Leaf1#show vxlan vni
VNI to VLAN Mapping for Vxlan1
VNI         VLAN       Source       Interface       802.1Q Tag
----------- ---------- ------------ --------------- ----------
10100       10         static       Ethernet7       untagged  
                                    Vxlan1          10        
10200       20         static       Ethernet8       untagged  
                                    Vxlan1          20        

VNI to dynamic VLAN Mapping for Vxlan1
VNI         VLAN       VRF            Source       
----------- ---------- -------------- ------------ 
50000       4094       TENANT-A       evpn         

Leaf1#
Leaf1#show vxlan vtep
Remote VTEPS for Vxlan1:

VTEP              Tunnel Type(s)
----------------- --------------
10.255.1.11       flood         
10.255.1.12       flood, unicast

Total number of remote VTEPS:  2
Leaf1#
Leaf1#show ip route vrf TENANT-A

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

 C        192.168.10.0/24 is directly connected, Vlan10
 C        192.168.20.0/24 is directly connected, Vlan20

Leaf1#
Leaf1#sh mac address-table
          Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports      Moves   Last Move
----    -----------       ----        -----      -----   ---------
   1    0001.0001.0001    STATIC      Cpu
  10    0001.0001.0001    STATIC      Cpu
  10    0050.7966.6801    DYNAMIC     Et7        1       0:18:06 ago
  10    0050.7966.6802    DYNAMIC     Vx1        1       0:03:19 ago
  20    0001.0001.0001    STATIC      Cpu
  20    0050.7966.6803    DYNAMIC     Et8        1       0:05:55 ago
  20    0050.7966.6804    DYNAMIC     Vx1        1       0:03:24 ago
4094    0001.0001.0001    STATIC      Cpu
Total Mac Addresses for this criterion: 8

          Multicast Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       ----        -----
Total Mac Addresses for this criterion: 0
Leaf1#
Leaf1#
Leaf1#
```



## Spine1

```
hostname Spine1
zerotouch disable
ip routing

! Loopbacks
interface Loopback0
  ip address 10.1.1.0/32
interface Loopback1
  ip address 10.255.1.0/32

! P2P links
interface Ethernet1
  no switchport
  ip address 10.1.0.0/31
interface Ethernet2
  no switchport
  ip address 10.1.0.2/31
interface Ethernet3
  no switchport
  ip address 10.1.0.4/31

! Underlay BGP
router bgp 65000
  router-id 10.1.1.0
  neighbor 10.1.0.1 remote-as 65210
  neighbor 10.1.0.3 remote-as 65211
  neighbor 10.1.0.5 remote-as 65212
  address-family ipv4
    neighbor 10.1.0.1 activate
    neighbor 10.1.0.3 activate
    neighbor 10.1.0.5 activate
    network 10.1.1.0/32
    network 10.255.1.0/32
    network 10.1.0.0/31
    network 10.1.0.2/31
    network 10.1.0.4/31
    maximum-paths 4
  exit

! EVPN overlay (RR/tranzit)
router bgp 65000
  neighbor 10.255.1.10 remote-as 65210
  neighbor 10.255.1.11 remote-as 65211
  neighbor 10.255.1.12 remote-as 65212
  neighbor 10.255.1.10 update-source Loopback1
  neighbor 10.255.1.11 update-source Loopback1
  neighbor 10.255.1.12 update-source Loopback1
  neighbor 10.255.1.10 ebgp-multihop 3
  neighbor 10.255.1.11 ebgp-multihop 3
  neighbor 10.255.1.12 ebgp-multihop 3
  address-family evpn
    neighbor 10.255.1.10 activate
    neighbor 10.255.1.11 activate
    neighbor 10.255.1.12 activate
    neighbor 10.255.1.10 send-community extended
    neighbor 10.255.1.11 send-community extended
    neighbor 10.255.1.12 send-community extended
    neighbor 10.255.1.10 next-hop-unchanged
    neighbor 10.255.1.11 next-hop-unchanged
    neighbor 10.255.1.12 next-hop-unchanged
  exit
end

```

## Spine2

```
hostname Spine2
zerotouch disable
ip routing

interface Loopback0
  ip address 10.1.1.1/32
interface Loopback1
  ip address 10.255.1.1/32

interface Ethernet1
  no switchport
  ip address 10.1.0.6/31
interface Ethernet2
  no switchport
  ip address 10.1.0.8/31
interface Ethernet3
  no switchport
  ip address 10.1.0.10/31

router bgp 65000
  router-id 10.1.1.1
  neighbor 10.1.0.7 remote-as 65210
  neighbor 10.1.0.9 remote-as 65211
  neighbor 10.1.0.11 remote-as 65212
  address-family ipv4
    neighbor 10.1.0.7 activate
    neighbor 10.1.0.9 activate
    neighbor 10.1.0.11 activate
    network 10.1.1.1/32
    network 10.255.1.1/32
    network 10.1.0.6/31
    network 10.1.0.8/31
    network 10.1.0.10/31
    maximum-paths 4
  exit

router bgp 65000
  neighbor 10.255.1.10 remote-as 65210
  neighbor 10.255.1.11 remote-as 65211
  neighbor 10.255.1.12 remote-as 65212
  neighbor 10.255.1.10 update-source Loopback1
  neighbor 10.255.1.11 update-source Loopback1
  neighbor 10.255.1.12 update-source Loopback1
  neighbor 10.255.1.10 ebgp-multihop 3
  neighbor 10.255.1.11 ebgp-multihop 3
  neighbor 10.255.1.12 ebgp-multihop 3
  address-family evpn
    neighbor 10.255.1.10 activate
    neighbor 10.255.1.11 activate
    neighbor 10.255.1.12 activate
    neighbor 10.255.1.10 send-community extended
    neighbor 10.255.1.11 send-community extended
    neighbor 10.255.1.12 send-community extended
    neighbor 10.255.1.10 next-hop-unchanged
    neighbor 10.255.1.11 next-hop-unchanged
    neighbor 10.255.1.12 next-hop-unchanged
  exit
end

```

## Leaf1

```
hostname Leaf1
zerotouch disable
ip routing
service routing protocols model multi-agent

! Anycast GW MAC
ip virtual-router mac-address 0001.0001.0001

! Loopbacks
interface Loopback0
  ip address 10.1.1.10/32
interface Loopback1
  ip address 10.255.1.10/32

! P2P to spines
interface Ethernet1
  no switchport
  ip address 10.1.0.1/31
interface Ethernet2
  no switchport
  ip address 10.1.0.7/31

! Access ports to clients
vlan 10
vlan 20
interface Ethernet7
  switchport
  switchport mode access
  switchport access vlan 10
  spanning-tree portfast
interface Ethernet8
  switchport
  switchport mode access
  switchport access vlan 20
  spanning-tree portfast

! SVI with Anycast GW
interface Vlan10
  vrf TENANT-A
  ip address virtual 192.168.10.254/24
interface Vlan20
  vrf TENANT-A
  ip address virtual 192.168.20.254/24

! VRF and L3VNI
vrf instance TENANT-A
ip routing vrf TENANT-A

! VTEP
interface Vxlan1
  vxlan source-interface Loopback1
  vxlan udp-port 4789
  vxlan vlan 10 vni 10100
  vxlan vlan 20 vni 10200
  vxlan vrf TENANT-A vni 50000

! Underlay BGP
router bgp 65210
  router-id 10.1.1.10
  neighbor 10.1.0.0 remote-as 65000
  neighbor 10.1.0.6 remote-as 65000
  address-family ipv4
    neighbor 10.1.0.0 activate
    neighbor 10.1.0.6 activate
    network 10.1.1.10/32
    network 10.255.1.10/32
    network 10.1.0.1/31
    network 10.1.0.7/31
    maximum-paths 4
  exit

! EVPN (L2/L3 VNI)
router bgp 65210
  neighbor 10.255.1.0 remote-as 65000
  neighbor 10.255.1.1 remote-as 65000
  neighbor 10.255.1.0 update-source Loopback1
  neighbor 10.255.1.1 update-source Loopback1
  neighbor 10.255.1.0 ebgp-multihop 3
  neighbor 10.255.1.1 ebgp-multihop 3

  vlan 10
    rd auto
    route-target both 10100:10100
    redistribute learned
  vlan 20
    rd auto
    route-target both 10200:10200
    redistribute learned

  vrf TENANT-A
    rd auto
    route-target import evpn 50000:50000
    route-target export evpn 50000:50000
    neighbor default next-hop-self received-evpn-routes route-type ip-prefix
    redistribute connected

  address-family evpn
    neighbor 10.255.1.0 activate
    neighbor 10.255.1.1 activate
    neighbor 10.255.1.0 send-community extended
    neighbor 10.255.1.1 send-community extended
  exit
end

```

## Leaf2

```
hostname Leaf2
zerotouch disable
ip routing
service routing protocols model multi-agent

ip virtual-router mac-address 0001.0001.0001

interface Loopback0
  ip address 10.1.1.11/32
interface Loopback1
  ip address 10.255.1.11/32

interface Ethernet1
  no switchport
  ip address 10.1.0.3/31
interface Ethernet2
  no switchport
  ip address 10.1.0.9/31

vlan 10
vlan 20

interface Vlan10
  vrf TENANT-A
  ip address virtual 192.168.10.254/24
interface Vlan20
  vrf TENANT-A
  ip address virtual 192.168.20.254/24

vrf instance TENANT-A
ip routing vrf TENANT-A

interface Vxlan1
  vxlan source-interface Loopback1
  vxlan udp-port 4789
  vxlan vlan 10 vni 10100
  vxlan vlan 20 vni 10200
  vxlan vrf TENANT-A vni 50000

router bgp 65211
  router-id 10.1.1.11
  neighbor 10.1.0.2 remote-as 65000
  neighbor 10.1.0.8 remote-as 65000
  address-family ipv4
    neighbor 10.1.0.2 activate
    neighbor 10.1.0.8 activate
    network 10.1.1.11/32
    network 10.255.1.11/32
    network 10.1.0.3/31
    network 10.1.0.9/31
    maximum-paths 4
  exit

router bgp 65211
  neighbor 10.255.1.0 remote-as 65000
  neighbor 10.255.1.1 remote-as 65000
  neighbor 10.255.1.0 update-source Loopback1
  neighbor 10.255.1.1 update-source Loopback1
  neighbor 10.255.1.0 ebgp-multihop 3
  neighbor 10.255.1.1 ebgp-multihop 3

  vlan 10
    rd auto
    route-target both 10100:10100
    redistribute learned
  vlan 20
    rd auto
    route-target both 10200:10200
    redistribute learned

  vrf TENANT-A
    rd auto
    route-target import evpn 50000:50000
    route-target export evpn 50000:50000
    neighbor default next-hop-self received-evpn-routes route-type ip-prefix
    redistribute connected

  address-family evpn
    neighbor 10.255.1.0 activate
    neighbor 10.255.1.1 activate
    neighbor 10.255.1.0 send-community extended
    neighbor 10.255.1.1 send-community extended
  exit
end

```

## Leaf3

```
hostname Leaf3
zerotouch disable
ip routing
service routing protocols model multi-agent

ip virtual-router mac-address 0001.0001.0001

interface Loopback0
  ip address 10.1.1.12/32
interface Loopback1
  ip address 10.255.1.12/32

interface Ethernet1
  no switchport
  ip address 10.1.0.5/31
interface Ethernet2
  no switchport
  ip address 10.1.0.11/31

! Access ports к клиентам
vlan 10
vlan 20
interface Ethernet7
  switchport
  switchport mode access
  switchport access vlan 10
  spanning-tree portfast
interface Ethernet8
  switchport
  switchport mode access
  switchport access vlan 20
  spanning-tree portfast

interface Vlan10
  vrf TENANT-A
  ip address virtual 192.168.10.254/24
interface Vlan20
  vrf TENANT-A
  ip address virtual 192.168.20.254/24

vrf instance TENANT-A
ip routing vrf TENANT-A

interface Vxlan1
  vxlan source-interface Loopback1
  vxlan udp-port 4789
  vxlan vlan 10 vni 10100
  vxlan vlan 20 vni 10200
  vxlan vrf TENANT-A vni 50000

router bgp 65212
  router-id 10.1.1.12
  neighbor 10.1.0.4 remote-as 65000
  neighbor 10.1.0.10 remote-as 65000
  address-family ipv4
    neighbor 10.1.0.4 activate
    neighbor 10.1.0.10 activate
    network 10.1.1.12/32
    network 10.255.1.12/32
    network 10.1.0.5/31
    network 10.1.0.11/31
    maximum-paths 4
  exit

router bgp 65212
  neighbor 10.255.1.0 remote-as 65000
  neighbor 10.255.1.1 remote-as 65000
  neighbor 10.255.1.0 update-source Loopback1
  neighbor 10.255.1.1 update-source Loopback1
  neighbor 10.255.1.0 ebgp-multihop 3
  neighbor 10.255.1.1 ebgp-multihop 3

  vlan 10
    rd auto
    route-target both 10100:10100
    redistribute learned
  vlan 20
    rd auto
    route-target both 10200:10200
    redistribute learned

  vrf TENANT-A
    rd auto
    route-target import evpn 50000:50000
    route-target export evpn 50000:50000
    neighbor default next-hop-self received-evpn-routes route-type ip-prefix
    redistribute connected

  address-family evpn
    neighbor 10.255.1.0 activate
    neighbor 10.255.1.1 activate
    neighbor 10.255.1.0 send-community extended
    neighbor 10.255.1.1 send-community extended
  exit
end

```








