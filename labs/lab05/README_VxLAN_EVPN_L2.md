Hometask 2.11 - VxLAN EVPN - Arista 4.29.2F
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



# Демонстрация работы схемы

Для демонстрации сделано 2 VxLAN, 10010 И 10020, ассоциированы с VLAN 10 И 20. 

<img width="781" height="300" alt="image" src="https://github.com/user-attachments/assets/12a124e3-8ddd-4dad-b976-70b426c6a304" />

<img width="748" height="226" alt="image" src="https://github.com/user-attachments/assets/832802a6-d592-47a0-a1f8-a7bc1745dcd2" />

```
Spine1#
Spine1#
Spine1#
Spine1#show bgp evpn summary
BGP summary information for VRF default
Router identifier 10.255.1.0, local AS number 65000
Neighbor Status Codes: m - Under maintenance
  Neighbor    V AS           MsgRcvd   MsgSent  InQ OutQ  Up/Down State   PfxRcd PfxAcc
  10.255.1.10 4 65210            539       528    0    0 02:24:25 Estab   4      4
  10.255.1.11 4 65211            542       551    0    0 02:24:25 Estab   2      2
  10.255.1.12 4 65212            543       530    0    0 02:24:25 Estab   4      4
Spine1#
Spine1#show bgp evpn route-type imet
BGP routing table information for VRF default
Router identifier 10.255.1.0, local AS number 65000
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
 * >      RD: 10.255.1.10:10 imet 10.255.1.10
                                 10.255.1.10           -       100     0       65210 i
 * >      RD: 10.255.1.10:20 imet 10.255.1.10
                                 10.255.1.10           -       100     0       65210 i
 * >      RD: 10.255.1.11:10 imet 10.255.1.11
                                 10.255.1.11           -       100     0       65211 i
 * >      RD: 10.255.1.11:20 imet 10.255.1.11
                                 10.255.1.11           -       100     0       65211 i
 * >      RD: 10.255.1.12:10 imet 10.255.1.12
                                 10.255.1.12           -       100     0       65212 i
 * >      RD: 10.255.1.12:20 imet 10.255.1.12
                                 10.255.1.12           -       100     0       65212 i
Spine1#
Spine1#show bgp evpn route-type mac-ip
BGP routing table information for VRF default
Router identifier 10.255.1.0, local AS number 65000
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
 * >      RD: 10.255.1.10:10 mac-ip 0050.7966.6801
                                 10.255.1.10           -       100     0       65210 i
 * >      RD: 10.255.1.12:10 mac-ip 0050.7966.6802
                                 10.255.1.12           -       100     0       65212 i
 * >      RD: 10.255.1.10:20 mac-ip 0050.7966.6808
                                 10.255.1.10           -       100     0       65210 i
 * >      RD: 10.255.1.12:20 mac-ip 0050.7966.6809
                                 10.255.1.12           -       100     0       65212 i
Spine1#
Spine1#show vxlan vtep
Spine1#
Spine1#show vxlan vni
Spine1#
Spine1#show vxlan address-table
          Vxlan Mac Address Table
----------------------------------------------------------------------

VLAN  Mac Address     Type      Prt  VTEP             Moves   Last Move
----  -----------     ----      ---  ----             -----   ---------
Total Remote Mac Addresses for this criterion: 0
Spine1#
Spine1#
Spine1#
```


```
Leaf1#
Leaf1#
Leaf1#
Leaf1#
Leaf1#show bgp evpn summary
BGP summary information for VRF default
Router identifier 10.255.1.10, local AS number 65210
Neighbor Status Codes: m - Under maintenance
  Neighbor   V AS           MsgRcvd   MsgSent  InQ OutQ  Up/Down State   PfxRcd PfxAcc
  10.255.1.0 4 65000            529       539    0    0 02:25:03 Estab   6      6
  10.255.1.1 4 65000            510       534    0    0 06:37:20 Estab   6      6
Leaf1#
Leaf1#show bgp evpn route-type imet
BGP routing table information for VRF default
Router identifier 10.255.1.10, local AS number 65210
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
 * >      RD: 10.255.1.10:10 imet 10.255.1.10
                                 -                     -       -       0       i
 * >      RD: 10.255.1.10:20 imet 10.255.1.10
                                 -                     -       -       0       i
 * >Ec    RD: 10.255.1.11:10 imet 10.255.1.11
                                 10.255.1.11           -       100     0       65000 65211 i
 *  ec    RD: 10.255.1.11:10 imet 10.255.1.11
                                 10.255.1.11           -       100     0       65000 65211 i
 * >Ec    RD: 10.255.1.11:20 imet 10.255.1.11
                                 10.255.1.11           -       100     0       65000 65211 i
 *  ec    RD: 10.255.1.11:20 imet 10.255.1.11
                                 10.255.1.11           -       100     0       65000 65211 i
 * >Ec    RD: 10.255.1.12:10 imet 10.255.1.12
                                 10.255.1.12           -       100     0       65000 65212 i
 *  ec    RD: 10.255.1.12:10 imet 10.255.1.12
                                 10.255.1.12           -       100     0       65000 65212 i
 * >Ec    RD: 10.255.1.12:20 imet 10.255.1.12
                                 10.255.1.12           -       100     0       65000 65212 i
 *  ec    RD: 10.255.1.12:20 imet 10.255.1.12
                                 10.255.1.12           -       100     0       65000 65212 i
Leaf1#
Leaf1#show bgp evpn route-type mac-ip
BGP routing table information for VRF default
Router identifier 10.255.1.10, local AS number 65210
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
 * >      RD: 10.255.1.10:10 mac-ip 0050.7966.6801
                                 -                     -       -       0       i
 * >Ec    RD: 10.255.1.12:10 mac-ip 0050.7966.6802
                                 10.255.1.12           -       100     0       65000 65212 i
 *  ec    RD: 10.255.1.12:10 mac-ip 0050.7966.6802
                                 10.255.1.12           -       100     0       65000 65212 i
 * >      RD: 10.255.1.10:20 mac-ip 0050.7966.6808
                                 -                     -       -       0       i
 * >Ec    RD: 10.255.1.12:20 mac-ip 0050.7966.6809
                                 10.255.1.12           -       100     0       65000 65212 i
 *  ec    RD: 10.255.1.12:20 mac-ip 0050.7966.6809
                                 10.255.1.12           -       100     0       65000 65212 i
Leaf1#
Leaf1#show vxlan vtep
Remote VTEPS for Vxlan1:

VTEP              Tunnel Type(s)
----------------- --------------
10.255.1.11       flood         
10.255.1.12       flood, unicast

Total number of remote VTEPS:  2
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
VNI       VLAN       VRF       Source       
--------- ---------- --------- ------------ 

Leaf1#
Leaf1#show vxlan address-table
          Vxlan Mac Address Table
----------------------------------------------------------------------

VLAN  Mac Address     Type      Prt  VTEP             Moves   Last Move
----  -----------     ----      ---  ----             -----   ---------
  10  0050.7966.6802  EVPN      Vx1  10.255.1.12      1       0:04:29 ago
  20  0050.7966.6809  EVPN      Vx1  10.255.1.12      1       0:01:46 ago
Total Remote Mac Addresses for this criterion: 2
Leaf1#
Leaf1#
Leaf1#
```





На Leaf 1 и 3, где присутствуют клиенты VxLan
<img width="404" height="169" alt="image" src="https://github.com/user-attachments/assets/b2341413-66a8-47cb-871e-10fafa66dbbd" />



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
   router-id 10.255.1.0
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
   router-id 10.255.1.1
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
   router-id 10.255.1.1
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
vlan 10,20
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
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 10100
   vxlan vlan 20 vni 10200
!
ip routing
!
router bgp 65211
   router-id 10.255.1.11
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
vlan 10,20
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
   description Client10.1
   switchport access vlan 10
!
interface Ethernet8
   description Client20.1
   switchport access vlan 20
!
interface Loopback0
   ip address 10.1.1.12/32
!
interface Loopback1
   ip address 10.255.1.12/32
!
interface Management1
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 10100
   vxlan vlan 20 vni 10200
!
ip routing
!
router bgp 65212
   router-id 10.255.1.12
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
end


```











