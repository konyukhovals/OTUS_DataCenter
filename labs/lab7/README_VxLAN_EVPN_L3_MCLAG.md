
Hometask 2.14 - VxLAN EVPN L3 + MC-LAG

# Наша топология
Сделано не хост а свич для LACP, потому что сущность с бондами не смог запихнуть в свой pnetlab. LACP невероятно глючит, очень много времени было потеряно на этом.

<img width="2260" height="1247" alt="image" src="https://github.com/user-attachments/assets/f739bab4-f2a3-48b9-8cbc-8b3ad23c9961" />

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
   description CLIENT_LACP_Left
   switchport access vlan 10
   mlag 10
   spanning-tree portfast
!
interface Port-Channel200
   description MLAG PEER-LINK to Leaf2
   switchport mode trunk
   switchport trunk group MLAG-PEER
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
   switchport access vlan 10
!
interface Ethernet5
   channel-group 10 mode active
!
interface Ethernet6
   description MLAG keepalive to Leaf1
   switchport access vlan 4093
   spanning-tree portfast
!
interface Ethernet7
   description MLAG peer-lik to Leaf2
   channel-group 200 mode active
!
interface Ethernet8
   description MLAG peer-lik to Leaf2
   channel-group 200 mode active
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
interface Vlan4093
   description MLAG keepalive SVI
   ip address 169.254.12.1/30
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
   peer-address 169.254.12.2
   peer-link Port-Channel200
   reload-delay mlag 300
   reload-delay non-mlag 330
!
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
eaf4#sh run
! Command: show running-config
! device: Leaf4 (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Leaf4
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
   description MLAG PEER-LINK to Leaf3
   switchport mode trunk
   switchport trunk group MLAG-PEER
!
interface Ethernet1
   no switchport
   ip address 10.1.0.7/31
!
interface Ethernet2
   no switchport
   ip address 10.1.0.15/31
!
interface Ethernet3
   no switchport
   ip address 10.1.0.23/31
!
interface Ethernet4
   switchport access vlan 20
!
interface Ethernet5
   channel-group 10 mode active
!
interface Ethernet6
   description MLAG keepalive to Leaf3
   switchport access vlan 4093
   spanning-tree portfast
!
interface Ethernet7
   description MLAG peer-lik to Leaf3
   channel-group 200 mode active
!
interface Ethernet8
   description MLAG peer-lik to Lea3
   switchport access vlan 20
   channel-group 200 mode active
!
interface Loopback0
   ip address 10.1.1.13/32
!
interface Loopback1
   ip address 10.255.1.13/32
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
   ip address 169.254.12.6/30
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
   peer-address 169.254.12.5
   peer-link Port-Channel200
   reload-delay mlag 300
   reload-delay non-mlag 330
!
ip route vrf TENANT 192.168.10.0/24 Null0
ip route vrf TENANT 192.168.20.0/24 Null0
!
router bgp 65213
   router-id 10.1.1.13
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
   neighbor 10.1.0.6 peer group UNDERLAY
   neighbor 10.1.0.6 remote-as 65000
   neighbor 10.1.0.14 peer group UNDERLAY
   neighbor 10.1.0.14 remote-as 65000
   neighbor 10.1.0.22 peer group UNDERLAY
   neighbor 10.1.0.22 remote-as 65000
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
      network 10.1.0.6/31
      network 10.1.0.14/31
      network 10.1.0.22/31
      network 10.1.1.13/32
      network 10.255.1.13/32
   !
   vrf TENANT
      rd 10.255.1.13:50000
      route-target import evpn 50000:50000
      route-target export evpn 50000:50000
      redistribute connected
!
end
Leaf4#
```
