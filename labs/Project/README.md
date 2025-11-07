Заключительная работа - два DC, BGP/BGP и OSPF/BGP, в DC1 - подключен ISP с default route

<img width="1634" height="870" alt="image" src="https://github.com/user-attachments/assets/0ffe741a-79b3-4035-8f62-3ecfb75fe007" />



<img width="1217" height="573" alt="image" src="https://github.com/user-attachments/assets/e9afe28c-f9a5-47a2-a7df-b10a5bbbea33" />

<img width="1192" height="768" alt="image" src="https://github.com/user-attachments/assets/98bbf859-70cc-4746-b497-d97820fe730a" />

<img width="602" height="375" alt="image" src="https://github.com/user-attachments/assets/69972141-dc87-4b8a-a9d0-36d6f2f70417" />







# DC1
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
   description to-Leaf1
   no switchport
   ip address 10.1.0.1/31
!
interface Ethernet2
   description to-Leaf2
   no switchport
   ip address 10.1.0.3/31
!
interface Ethernet3
   description to-Leaf3
   no switchport
   ip address 10.1.0.5/31
!
interface Ethernet4
   description DCI to Spine1_2
   no switchport
   ip address 172.16.0.0/31
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
ip prefix-list VTEP-DC1 seq 5 permit 10.255.1.0/24
!
ip route 10.255.2.0/32 172.16.0.1
!
route-map DCI-OUT permit 10
   match ip address prefix-list VTEP-DC1
!
router bgp 65000
   router-id 10.255.1.0
   no bgp default ipv4-unicast
   maximum-paths 8 ecmp 8
   neighbor 10.1.0.0 remote-as 65210
   neighbor 10.1.0.1 remote-as 65210
   neighbor 10.1.0.2 remote-as 65211
   neighbor 10.1.0.3 remote-as 65211
   neighbor 10.1.0.4 remote-as 65212
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
   neighbor 10.255.2.0 remote-as 65300
   neighbor 10.255.2.0 next-hop-unchanged
   neighbor 10.255.2.0 update-source Loopback1
   neighbor 10.255.2.0 ebgp-multihop 5
   neighbor 10.255.2.0 send-community extended
   neighbor 10.255.2.1 remote-as 65300
   neighbor 10.255.2.1 next-hop-unchanged
   neighbor 10.255.2.1 update-source Loopback1
   neighbor 10.255.2.1 send-community extended
   !
   address-family evpn
      neighbor 10.255.1.10 activate
      neighbor 10.255.1.11 activate
      neighbor 10.255.1.12 activate
      neighbor 10.255.2.0 activate
      neighbor 10.255.2.1 activate
   !
   address-family ipv4
      neighbor 10.1.0.0 activate
      neighbor 10.1.0.1 activate
      neighbor 10.1.0.2 activate
      neighbor 10.1.0.3 activate
      neighbor 10.1.0.4 activate
      neighbor 10.1.0.5 activate
      neighbor 10.255.2.0 activate
      no neighbor 172.16.0.1 activate
      network 10.1.0.0/31
      network 10.1.0.2/31
      network 10.1.0.4/31
      network 10.1.1.0/32
      network 10.255.1.0/32
      network 172.16.0.0/31
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
   description to-Leaf1
   no switchport
   ip address 10.1.0.7/31
!
interface Ethernet2
   description to-Leaf2
   no switchport
   ip address 10.1.0.9/31
!
interface Ethernet3
   description to-Leaf3
   no switchport
   ip address 10.1.0.11/31
!
interface Ethernet4
   description DCI to Spine2_2
   no switchport
   ip address 172.16.0.2/31
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
   no bgp default ipv4-unicast
   maximum-paths 8 ecmp 8
   neighbor 10.1.0.6 remote-as 65210
   neighbor 10.1.0.7 remote-as 65210
   neighbor 10.1.0.8 remote-as 65211
   neighbor 10.1.0.9 remote-as 65211
   neighbor 10.1.0.10 remote-as 65212
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
   neighbor 10.255.2.0 remote-as 65300
   neighbor 10.255.2.0 next-hop-unchanged
   neighbor 10.255.2.0 update-source Loopback1
   neighbor 10.255.2.0 send-community extended
   neighbor 10.255.2.1 remote-as 65300
   neighbor 10.255.2.1 next-hop-unchanged
   neighbor 10.255.2.1 update-source Loopback1
   neighbor 10.255.2.1 send-community extended
   neighbor 172.16.0.3 remote-as 65300
   aggregate-address 10.255.1.0/24 summary-only
   !
   address-family evpn
      neighbor 10.255.1.10 activate
      neighbor 10.255.1.11 activate
      neighbor 10.255.1.12 activate
   !
   address-family ipv4
      neighbor 10.1.0.6 activate
      neighbor 10.1.0.7 activate
      neighbor 10.1.0.8 activate
      neighbor 10.1.0.9 activate
      neighbor 10.1.0.10 activate
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
   name TENANT-A_10
!
vlan 20
   name TENANT-A_20
!
vrf instance ISP
!
vrf instance TENANT
!
vrf instance TENANT-A
!
interface Ethernet1
   description to-Spine1
   no switchport
   ip address 10.1.0.0/31
!
interface Ethernet2
   description to-Spine2
   no switchport
   ip address 10.1.0.6/31
!
interface Ethernet3
!
interface Ethernet4
!
interface Ethernet5
!
interface Ethernet6
   description ISP
   no switchport
   vrf ISP
   ip address 210.1.1.0/31
!
interface Ethernet7
   description host-VLAN10
   switchport access vlan 10
   spanning-tree portfast
!
interface Ethernet8
   description host-VLAN20
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
   vxlan vlan 10 vni 1010
   vxlan vlan 20 vni 1020
   vxlan vrf ISP vni 8888
   vxlan vrf TENANT vni 5000
   vxlan vrf TENANT-A vni 50000
!
ip virtual-router mac-address 00:1c:73:00:00:99
!
ip routing
ip routing vrf ISP
ip routing vrf TENANT
ip routing vrf TENANT-A
!
ip route vrf ISP 0.0.0.0/0 210.1.1.1
ip route vrf TENANT-A 192.168.10.0/24 Null0
ip route vrf TENANT-A 192.168.20.0/24 Null0
!
router bgp 65210
   router-id 10.1.1.10
   no bgp default ipv4-unicast
   rd auto
   maximum-paths 2 ecmp 2
   neighbor 10.1.0.0 remote-as 65000
   neighbor 10.1.0.1 remote-as 65000
   neighbor 10.1.0.6 remote-as 65000
   neighbor 10.1.0.7 remote-as 65000
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
      rd 10.255.1.10:1010
      route-target import export evpn domain all 65000:1010
      route-target import export evpn domain all 65000:1020
      route-target import 65210:1010
      route-target import 65300:1010
      route-target import export evpn domain all 65300:5000
      route-target import evpn domain remote 65210:1010
      route-target export 65210:1010
      route-target export 65300:1010
      route-target export evpn domain remote 65210:1010
      redistribute learned
   !
   vlan 20
      rd 10.255.1.10:1020
      route-target import export evpn domain all 65000:1010
      route-target import export evpn domain all 65000:1020
      route-target import 65300:1020
      route-target import export evpn domain all 65300:5000
      route-target export 65300:1020
      redistribute learned
   !
   address-family evpn
      neighbor 10.255.1.0 activate
      neighbor 10.255.1.1 activate
   !
   address-family ipv4
      neighbor 10.1.0.0 activate
      neighbor 10.1.0.1 activate
      neighbor 10.1.0.6 activate
      neighbor 10.1.0.7 activate
      network 10.1.0.0/31
      network 10.1.0.6/31
      network 10.1.1.10/32
      network 10.255.1.10/32
   !
   vrf ISP
      rd 10.255.1.10:8888
      route-target import evpn 65000:8888
      route-target import evpn 65000:50000
      route-target import evpn 65212:8888
      route-target import evpn 65300:8888
      route-target export evpn 65000:8888
      route-target export evpn 65212:8888
      route-target export evpn 65300:8888
      redistribute static
   !
   vrf TENANT-A
      rd 10.255.1.10:50000
      route-target import 65210:50000
      route-target import evpn 65000:5000
      route-target import evpn 65000:8888
      route-target import evpn 65000:50000
      route-target import evpn 65300:5000
      route-target import evpn 65300:50000
      route-target export 65210:50000
      route-target export evpn 65000:5000
      route-target export evpn 65000:50000
      route-target export evpn 65300:5000
      redistribute connected
!
end
Leaf1#
```

## Leaf2
```
Leaf2#sh ru
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
   name TENANT-A_10
!
vlan 20
   name TENANT-A_20
!
vrf instance TENANT
!
vrf instance TENANT-A
!
interface Ethernet1
   description to-Spine1
   no switchport
   ip address 10.1.0.2/31
!
interface Ethernet2
   description to-Spine2
   no switchport
   ip address 10.1.0.8/31
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
   description host-VLAN10
   switchport access vlan 10
!
interface Ethernet8
!
interface Ethernet9
!
interface Ethernet10
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
   vxlan vlan 10 vni 1010
   vxlan vlan 20 vni 1020
   vxlan vrf TENANT vni 5000
   vxlan vrf TENANT-A vni 50000
!
ip virtual-router mac-address 00:1c:73:00:00:99
!
ip routing
ip routing vrf TENANT
ip routing vrf TENANT-A
!
ip route vrf TENANT-A 192.168.10.0/24 Null0
ip route vrf TENANT-A 192.168.20.0/24 Null0
!
router bgp 65211
   router-id 10.1.1.11
   no bgp default ipv4-unicast
   rd auto
   maximum-paths 2 ecmp 2
   neighbor 10.1.0.2 remote-as 65000
   neighbor 10.1.0.3 remote-as 65000
   neighbor 10.1.0.8 remote-as 65000
   neighbor 10.1.0.9 remote-as 65000
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
      rd 10.255.1.11:1010
      route-target both 10100:10100
      route-target import export evpn domain all 65000:1010
      route-target import export evpn domain all 65000:1020
      route-target import 65300:1010
      route-target import export evpn domain all 65300:5000
      route-target export 65300:1010
      redistribute learned
   !
   vlan 20
      rd 10.255.1.11:1020
      route-target both 10200:10200
      route-target import export evpn domain all 65000:1010
      route-target import export evpn domain all 65000:1020
      route-target import 65300:1020
      route-target import export evpn domain all 65300:5000
      route-target export 65300:1020
      redistribute learned
   !
   address-family evpn
      neighbor 10.255.1.0 activate
      neighbor 10.255.1.1 activate
   !
   address-family ipv4
      neighbor 10.1.0.2 activate
      neighbor 10.1.0.3 activate
      neighbor 10.1.0.8 activate
      neighbor 10.1.0.9 activate
      network 10.1.0.2/31
      network 10.1.0.8/31
      network 10.1.1.11/32
      network 10.255.1.11/32
   !
   vrf TENANT
      rd 10.255.1.11:5000
      route-target import evpn 65000:5000
      route-target import evpn 65300:5000
      route-target export evpn 65000:5000
      redistribute connected
   !
   vrf TENANT-A
      rd 10.255.1.11:50000
      route-target import 50000:50000
      route-target import evpn 65000:5000
      route-target export 50000:50000
      route-target export evpn 65000:5000
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
   name TENANT-A_10
!
vlan 20
   name TENANT-A_20
!
vrf instance TENANT
!
vrf instance TENANT-A
!
interface Ethernet1
   description to-Spine1
   no switchport
   ip address 10.1.0.4/31
!
interface Ethernet2
   description to-Spine2
   no switchport
   ip address 10.1.0.10/31
!
interface Ethernet3
   no switchport
   ip address 10.3.0.0/31
!
interface Ethernet4
   description to-Spine1-secondary
   no switchport
   ip address 10.1.0.12/31
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
   description host-VLAN20
   switchport access vlan 20
   spanning-tree portfast
!
interface Loopback0
   ip address 10.1.1.12/32
!
interface Loopback1
   description VTEP
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
   vxlan vlan 10 vni 1010
   vxlan vlan 20 vni 1020
   vxlan vrf TENANT-A vni 5000
!
ip virtual-router mac-address 00:1c:73:00:00:99
!
ip routing
ip routing vrf TENANT
ip routing vrf TENANT-A
!
ip prefix-list DCI-IN seq 10 permit 10.255.2.0/24
ip prefix-list DCI-OUT seq 10 permit 10.255.1.0/24
!
ip route 10.255.1.0/24 Null0
ip route 10.255.1.12/32 10.3.0.1
ip route 10.255.2.10/32 10.3.0.1
ip route vrf TENANT-A 192.168.10.0/24 Null0
ip route vrf TENANT-A 192.168.20.0/24 Null0
!
route-map DCI-IN permit 10
   match ip address prefix-list DCI-IN
!
route-map DCI-OUT permit 10
   match ip address prefix-list DCI-OUT
!
router bgp 65212
   router-id 10.1.1.12
   no bgp default ipv4-unicast
   rd auto
   maximum-paths 4 ecmp 4
   neighbor 10.1.0.5 remote-as 65000
   neighbor 10.1.0.11 remote-as 65000
   neighbor 10.1.0.13 remote-as 65000
   neighbor 10.3.0.1 remote-as 65300
   neighbor 10.3.0.1 send-community extended
   neighbor 10.255.1.0 remote-as 65000
   neighbor 10.255.1.0 update-source Loopback1
   neighbor 10.255.1.0 ebgp-multihop 5
   neighbor 10.255.1.0 send-community extended
   neighbor 10.255.1.1 remote-as 65000
   neighbor 10.255.1.1 update-source Loopback1
   neighbor 10.255.1.1 ebgp-multihop 5
   neighbor 10.255.1.1 send-community extended
   neighbor 10.255.2.10 remote-as 65300
   neighbor 10.255.2.10 update-source Loopback1
   neighbor 10.255.2.10 ebgp-multihop 10
   neighbor 10.255.2.10 send-community extended
   redistribute connected
   !
   vlan 10
      rd 10.255.1.12:1010
      route-target import export evpn domain all 65000:1010
      route-target import export evpn domain all 65000:1020
      route-target import 65300:1010
      route-target import export evpn domain all 65300:5000
      route-target export 65300:1010
      redistribute learned
   !
   vlan 20
      rd 10.255.1.12:1020
      route-target import export evpn domain all 65000:1010
      route-target import export evpn domain all 65000:1020
      route-target import 65300:1010
      route-target import 65300:1020
      route-target import export evpn domain all 65300:5000
      route-target export 65300:1020
      redistribute learned
   !
   address-family evpn
      neighbor 10.255.1.0 activate
      neighbor 10.255.1.1 activate
      neighbor 10.255.2.10 activate
   !
   address-family ipv4
      neighbor 10.1.0.5 activate
      neighbor 10.1.0.11 activate
      neighbor 10.1.0.13 activate
      neighbor 10.3.0.1 activate
      neighbor 10.3.0.1 route-map DCI-IN in
      neighbor 10.3.0.1 route-map DCI-OUT out
      network 10.1.1.12/32
      network 10.255.1.12/32
      network 10.255.1.0/24
      network 10.255.2.0/24
   !
   vrf TENANT-A
      rd 10.255.1.12:5000
      route-target import 50000:50000
      route-target import evpn 65000:5000
      route-target import evpn 65300:5000
      route-target export 50000:50000
      route-target export evpn 65000:5000
      route-target export evpn 65300:5000
      redistribute connected
!
end
Leaf3#
```

## ISP
```
ISP#sh run
! Command: show running-config
! device: ISP (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model ribd
!
hostname ISP
!
spanning-tree mode mstp
!
interface Ethernet1
   no switchport
   ip address 8.8.8.254/24
!
interface Ethernet2
!
interface Ethernet3
!
interface Ethernet4
!
interface Ethernet5
!
interface Ethernet6
   no switchport
   ip address 210.1.1.1/31
!
interface Ethernet7
!
interface Ethernet8
!
interface Management1
!
ip routing
!
ip route 0.0.0.0/0 210.1.1.0
!
end
ISP#
```


# DC2
## Spine1_2
```
Spine1_2#sh run
! Command: show running-config
! device: Spine1-2 (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Spine1_2
!
spanning-tree mode mstp
!
interface Ethernet1
   description to-Leaf1_2
   no switchport
   ip address 10.2.0.1/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.10
!
interface Ethernet2
   description to-Leaf2_2
   no switchport
   ip address 10.2.0.7/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.10
!
interface Ethernet3
   description to-Leaf3_2
   no switchport
   ip address 10.2.0.4/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.10
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
   ip address 10.1.2.0/32
   ip ospf area 0.0.0.10
!
interface Loopback1
   ip address 10.255.2.0/32
   ip ospf area 0.0.0.10
!
interface Management1
!
ip routing
!
ip prefix-list DC2-VTEP-LO seq 5 permit 10.255.2.0/24 ge 32
ip prefix-list VTEP-DC2 seq 5 permit 10.255.2.0/24
!
ip route 10.255.1.0/32 172.16.0.0
!
route-map DCI-OUT permit 10
   match ip address prefix-list VTEP-DC2
!
route-map OSPF-TO-BGP-VTEP permit 10
   match ip address prefix-list DC2-VTEP-LO
!
router bgp 65300
   router-id 10.255.2.0
   no bgp default ipv4-unicast
   bgp route-reflector preserve-attributes
   neighbor EVPN-PEERS peer group
   neighbor EVPN-PEERS remote-as 65300
   neighbor EVPN-PEERS next-hop-unchanged
   neighbor EVPN-PEERS update-source Loopback1
   neighbor EVPN-PEERS route-reflector-client
   neighbor EVPN-PEERS send-community extended
   neighbor 10.255.2.10 peer group EVPN-PEERS
   neighbor 10.255.2.10 remote-as 65300
   neighbor 10.255.2.10 update-source Loopback1
   neighbor 10.255.2.10 route-reflector-client
   neighbor 10.255.2.10 send-community extended
   neighbor 10.255.2.11 peer group EVPN-PEERS
   neighbor 10.255.2.11 remote-as 65300
   neighbor 10.255.2.11 update-source Loopback1
   neighbor 10.255.2.11 route-reflector-client
   neighbor 10.255.2.11 send-community extended
   neighbor 10.255.2.12 peer group EVPN-PEERS
   neighbor 10.255.2.12 remote-as 65300
   neighbor 10.255.2.12 update-source Loopback1
   neighbor 10.255.2.12 route-reflector-client
   neighbor 10.255.2.12 send-community extended
   !
   address-family evpn
      no bgp next-hop-unchanged
      neighbor EVPN-PEERS activate
      neighbor 10.255.2.10 activate
      neighbor 10.255.2.11 activate
      neighbor 10.255.2.12 activate
   !
   address-family ipv4
      network 10.255.2.0/32
      network 10.255.2.10/32
      network 10.255.2.11/32
      network 10.255.2.12/32
      network 10.255.2.0/24 route-map DCI-OUT
!
router ospf 10
   router-id 10.1.2.0
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet3
   passive-interface Loopback0
   passive-interface Loopback1
   network 10.0.0.0/8 area 0.0.0.10
   network 10.1.2.0/32 area 0.0.0.10
   network 10.2.0.0/24 area 0.0.0.10
   max-lsa 12000
!
end
Spine1_2#
```

## Spine2_2
```

Spine2_2#sh run
! Command: show running-config
! device: Spine2-2 (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Spine2_2
!
spanning-tree mode mstp
!
interface Ethernet1
   description to-Leaf1_2
   no switchport
   ip address 10.2.0.3/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.10
!
interface Ethernet2
   description to-Leaf2_2
   no switchport
   ip address 10.2.0.9/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.10
!
interface Ethernet3
   description to-Leaf3_2
   no switchport
   ip address 10.2.0.11/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.10
!
interface Ethernet4
   description DCI to Spine2 (DC1)
   no switchport
   ip address 172.16.0.3/31
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
   ip address 10.1.2.1/32
   ip ospf area 0.0.0.10
!
interface Loopback1
   ip address 10.255.2.1/32
   ip ospf area 0.0.0.10
!
interface Management1
!
ip routing
!
router bgp 65300
   router-id 10.255.2.1
   no bgp default ipv4-unicast
   bgp route-reflector preserve-attributes
   neighbor EVPN-PEERS peer group
   neighbor EVPN-PEERS remote-as 65300
   neighbor EVPN-PEERS next-hop-unchanged
   neighbor EVPN-PEERS update-source Loopback1
   neighbor EVPN-PEERS route-reflector-client
   neighbor EVPN-PEERS send-community extended
   neighbor 10.255.2.10 peer group EVPN-PEERS
   neighbor 10.255.2.10 remote-as 65300
   neighbor 10.255.2.10 update-source Loopback1
   neighbor 10.255.2.10 route-reflector-client
   neighbor 10.255.2.10 send-community extended
   neighbor 10.255.2.11 peer group EVPN-PEERS
   neighbor 10.255.2.11 remote-as 65300
   neighbor 10.255.2.11 update-source Loopback1
   neighbor 10.255.2.11 route-reflector-client
   neighbor 10.255.2.11 send-community extended
   neighbor 10.255.2.12 peer group EVPN-PEERS
   neighbor 10.255.2.12 remote-as 65300
   neighbor 10.255.2.12 update-source Loopback1
   neighbor 10.255.2.12 route-reflector-client
   neighbor 10.255.2.12 send-community extended
   aggregate-address 10.255.2.0/24 summary-only
   !
   address-family evpn
      no bgp next-hop-unchanged
      neighbor EVPN-PEERS activate
      neighbor 10.255.2.10 activate
      neighbor 10.255.2.11 activate
      neighbor 10.255.2.12 activate
!
router ospf 10
   router-id 10.1.2.1
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet3
   passive-interface Loopback0
   passive-interface Loopback1
   network 10.0.0.0/8 area 0.0.0.10
   network 10.1.2.1/32 area 0.0.0.10
   network 10.2.0.0/24 area 0.0.0.10
   max-lsa 12000
!
end
Spine2_2#
```

## Leaf1_2
```
Leaf1_2#sh run
! Command: show running-config
! device: Leaf1-2 (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Leaf1_2
!
spanning-tree mode mstp
!
vlan 10
   name TENANT-A_10
!
vlan 20
   name TENANT-A_20
!
vrf instance TENANT
!
interface Ethernet1
   description to-Spine1_2
   no switchport
   ip address 10.2.0.0/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.10
!
interface Ethernet2
   description to-Spine2_2
   no switchport
   ip address 10.2.0.2/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.10
!
interface Ethernet3
   no switchport
   ip address 10.3.0.1/31
!
interface Ethernet4
   description to-Spine1_2-secondary
   no switchport
   ip address 10.2.0.12/31
!
interface Ethernet5
!
interface Ethernet6
!
interface Ethernet7
   description host-VLAN10
   switchport access vlan 10
!
interface Ethernet8
   description host-VLAN20
   switchport access vlan 20
!
interface Loopback0
   ip address 10.1.2.10/32
   ip ospf area 0.0.0.10
!
interface Loopback1
   description VTEP
   ip address 10.255.2.10/32
   ip ospf area 0.0.0.10
!
interface Management1
!
interface Vlan10
   vrf TENANT
   ip address virtual 192.168.10.254/24
!
interface Vlan20
   vrf TENANT
   ip address virtual 192.168.20.254/24
!
interface Vxlan1
   description VTEP
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 1010
   vxlan vlan 20 vni 1020
   vxlan vrf TENANT vni 5000
!
ip virtual-router mac-address 00:1c:73:00:00:99
!
ip routing
ip routing vrf TENANT
!
ip prefix-list DCI-IN seq 10 permit 10.255.1.0/24
ip prefix-list DCI-OSPF-REDIST seq 10 permit 10.255.1.0/24
ip prefix-list DCI-OUT seq 10 permit 10.255.2.0/24
!
ip route 10.255.2.0/24 Null0
ip route vrf TENANT 192.168.10.0/24 Null0
ip route vrf TENANT 192.168.20.0/24 Null0
!
route-map DCI-IN permit 10
   match ip address prefix-list DCI-IN
!
route-map DCI-OSPF-REDIST permit 10
   match ip address prefix-list DCI-OSPF-REDIST
!
route-map DCI-OUT permit 10
   match ip address prefix-list DCI-OUT
!
router bgp 65300
   router-id 10.255.2.10
   no bgp default ipv4-unicast
   rd auto
   bgp route-reflector preserve-attributes
   maximum-paths 4 ecmp 4
   neighbor SPINES peer group
   neighbor SPINES remote-as 65300
   neighbor SPINES update-source Loopback1
   neighbor SPINES send-community extended
   neighbor 10.3.0.0 remote-as 65212
   neighbor 10.255.1.12 remote-as 65212
   neighbor 10.255.1.12 update-source Loopback1
   neighbor 10.255.1.12 ebgp-multihop 10
   neighbor 10.255.1.12 send-community extended
   neighbor 10.255.2.0 peer group SPINES
   neighbor 10.255.2.0 remote-as 65300
   neighbor 10.255.2.0 next-hop-self
   neighbor 10.255.2.0 update-source Loopback1
   neighbor 10.255.2.0 send-community extended
   neighbor 10.255.2.1 peer group SPINES
   neighbor 10.255.2.1 remote-as 65300
   neighbor 10.255.2.1 next-hop-self
   neighbor 10.255.2.1 update-source Loopback1
   neighbor 10.255.2.1 send-community extended
   redistribute connected
   !
   vlan 10
      rd 10.255.2.10:1010
      route-target import export evpn domain all 65212:1010
      route-target import export evpn domain all 65300:1010
      redistribute learned
   !
   vlan 20
      rd 10.255.2.10:1020
      route-target import export evpn domain all 65212:1020
      route-target import export evpn domain all 65300:1020
      redistribute learned
   !
   address-family evpn
      neighbor SPINES activate
      neighbor 10.255.1.12 activate
      neighbor 10.255.2.0 activate
      neighbor 10.255.2.1 activate
   !
   address-family ipv4
      neighbor 10.3.0.0 activate
      neighbor 10.3.0.0 route-map DCI-IN in
      neighbor 10.3.0.0 route-map DCI-OUT out
      network 10.255.2.0/24
   !
   vrf TENANT
      rd 10.255.2.10:5000
      route-target import 65212:5000
      route-target import 65212:8888
      route-target import evpn 65212:5000
      route-target import evpn 65212:8888
      route-target import evpn 65300:5000
      route-target import evpn 65300:8888
      route-target export 65212:5000
      route-target export evpn 65212:5000
      route-target export evpn 65300:5000
      redistribute connected
!
router ospf 10
   router-id 10.1.2.10
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet4
   passive-interface Loopback0
   passive-interface Loopback1
   redistribute bgp route-map DCI-OSPF-REDIST
   network 10.0.0.0/8 area 0.0.0.10
   network 10.1.2.10/32 area 0.0.0.10
   network 10.2.0.0/30 area 0.0.0.10
   network 10.2.0.12/31 area 0.0.0.10
   max-lsa 12000
!
end
Leaf1_2#
```

## Leaf2_2
```
Leaf2_2#sh run
! Command: show running-config
! device: Leaf2-2 (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Leaf2_2
!
spanning-tree mode mstp
!
vlan 10
   name TENANT-A_10
!
vlan 20
   name TENANT-A_20
!
vrf instance TENANT
!
vrf instance TENANT-A
!
interface Ethernet1
   description to-Spine1_2
   no switchport
   ip address 10.2.0.6/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.10
!
interface Ethernet2
   description to-Spine2_2
   no switchport
   ip address 10.2.0.8/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.10
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
!
interface Ethernet8
   switchport access vlan 20
!
interface Loopback0
   ip address 10.1.2.11/32
   ip ospf area 0.0.0.10
!
interface Loopback1
   ip address 10.255.2.11/32
   ip ospf area 0.0.0.10
!
interface Management1
!
interface Vlan10
   vrf TENANT
   ip address virtual 192.168.10.254/24
!
interface Vlan20
   vrf TENANT
   ip address virtual 192.168.20.254/24
!
interface Vxlan1
   description VTEP
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 1010
   vxlan vlan 20 vni 1020
   vxlan vrf TENANT vni 5000
!
ip virtual-router mac-address 00:1c:73:00:00:99
!
ip routing
ip routing vrf TENANT
no ip routing vrf TENANT-A
!
ip route vrf TENANT 192.168.10.0/24 Null0
ip route vrf TENANT 192.168.20.0/24 Null0
!
router bgp 65300
   router-id 10.255.2.11
   no bgp default ipv4-unicast
   rd auto
   bgp route-reflector preserve-attributes
   neighbor SPINES peer group
   neighbor SPINES remote-as 65300
   neighbor SPINES update-source Loopback1
   neighbor SPINES send-community extended
   neighbor 10.255.2.0 peer group SPINES
   neighbor 10.255.2.0 remote-as 65300
   neighbor 10.255.2.0 update-source Loopback1
   neighbor 10.255.2.0 send-community extended
   neighbor 10.255.2.1 peer group SPINES
   neighbor 10.255.2.1 remote-as 65300
   neighbor 10.255.2.1 update-source Loopback1
   neighbor 10.255.2.1 send-community extended
   redistribute connected
   !
   vlan 10
      rd 10.255.2.11:1010
      route-target import export evpn domain all 65212:1010
      route-target import export evpn domain all 65300:1010
      redistribute learned
   !
   vlan 20
      rd 10.255.2.11:1020
      route-target import export evpn domain all 65212:1020
      route-target import export evpn domain all 65300:1020
      redistribute learned
   !
   address-family evpn
      neighbor SPINES activate
      neighbor 10.255.2.0 activate
      neighbor 10.255.2.1 activate
   !
   vrf TENANT
      rd 10.255.2.11:5000
      route-target import evpn 65300:5000
      route-target export evpn 65300:5000
      redistribute connected
!
router ospf 10
   router-id 10.1.2.11
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   passive-interface Loopback0
   passive-interface Loopback1
   network 10.0.0.0/8 area 0.0.0.10
   network 10.1.2.11/32 area 0.0.0.10
   network 10.2.0.6/31 area 0.0.0.10
   network 10.2.0.8/31 area 0.0.0.10
   max-lsa 12000
!
end
Leaf2_2#
```

## Leaf3_2
```
Leaf3_2#sh run
! Command: show running-config
! device: Leaf3-2 (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Leaf3_2
!
spanning-tree mode mstp
!
vlan 10
   name TENANT-A_10
!
vlan 20
   name TENANT-A_20
!
vrf instance TENANT
!
vrf instance TENANT-A
!
interface Ethernet1
   description to-Spine1_2
   no switchport
   ip address 10.2.0.5/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.10
!
interface Ethernet2
   description to-Spine2_2
   no switchport
   ip address 10.2.0.12/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.10
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
!
interface Ethernet8
   description host-VLAN20
   switchport access vlan 20
!
interface Loopback0
   ip address 10.1.2.12/32
   ip ospf area 0.0.0.10
!
interface Loopback1
   ip address 10.255.2.12/32
   ip ospf area 0.0.0.10
!
interface Management1
!
interface Vlan10
   vrf TENANT
   ip address virtual 192.168.10.254/24
!
interface Vlan20
   vrf TENANT
   ip address virtual 192.168.20.254/24
!
interface Vxlan1
   description VTEP
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 1010
   vxlan vlan 20 vni 1020
   vxlan vrf TENANT vni 5000
!
ip virtual-router mac-address 00:1c:73:00:00:99
!
ip routing
ip routing vrf TENANT
no ip routing vrf TENANT-A
!
ip route vrf TENANT 192.168.10.0/24 Null0
ip route vrf TENANT 192.168.20.0/24 Null0
!
router bgp 65300
   router-id 10.255.2.12
   no bgp default ipv4-unicast
   rd auto
   bgp route-reflector preserve-attributes
   neighbor SPINES peer group
   neighbor SPINES remote-as 65300
   neighbor SPINES update-source Loopback1
   neighbor SPINES send-community extended
   neighbor 10.255.2.0 peer group SPINES
   neighbor 10.255.2.0 remote-as 65300
   neighbor 10.255.2.0 update-source Loopback1
   neighbor 10.255.2.0 send-community extended
   neighbor 10.255.2.1 peer group SPINES
   neighbor 10.255.2.1 remote-as 65300
   neighbor 10.255.2.1 update-source Loopback1
   neighbor 10.255.2.1 send-community extended
   redistribute connected
   !
   vlan 10
      rd 10.255.2.12:1010
      route-target import 65000:1010
      route-target import export evpn domain all 65212:1010
      route-target import 65300:1010
      route-target export 65000:1010
      route-target export 65300:1010
      redistribute learned
   !
   vlan 20
      rd 10.255.2.12:1020
      route-target import 65000:1020
      route-target import 65300:1020
      route-target export 65000:1020
      route-target export 65300:1020
      redistribute learned
   !
   address-family evpn
      neighbor SPINES activate
      neighbor 10.255.2.0 activate
      neighbor 10.255.2.1 activate
   !
   vrf TENANT
      rd 10.255.2.12:5000
      route-target import 65212:5000
      route-target import evpn 65000:5000
      route-target import evpn 65212:5000
      route-target import evpn 65300:5000
      route-target export 65212:5000
      route-target export evpn 65000:5000
      route-target export evpn 65212:5000
      route-target export evpn 65300:5000
      redistribute connected
!
router ospf 10
   router-id 10.1.2.12
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   passive-interface Loopback0
   passive-interface Loopback1
   network 10.0.0.0/8 area 0.0.0.10
   network 10.1.2.12/32 area 0.0.0.10
   network 10.2.0.10/31 area 0.0.0.10
   network 10.2.0.12/31 area 0.0.0.10
   max-lsa 12000
!
end
Leaf3_2#
```










234
