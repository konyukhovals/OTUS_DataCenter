Hometask 2.11 - VxLAN. L2 VNI

# Наша топология

![image](https://github.com/user-attachments/assets/7299341a-677b-4463-b533-b56725899571)


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
|Leaf 4|10.1.1.13/32|10.255.1.13/32|

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


# Демонстрация работы схемы

Поскольку у нас обрезанная эмуляция, насколько я понял, мы сделали схему Flood-and-Lear VXLAN, потому что EVPN VXLAN не поддерживается на образах, с которыми мы работаем.

я вывел в влане 10 два ПК - 172.16.0.1/2:

![image](https://github.com/user-attachments/assets/624a4381-818b-4054-95f5-f7d94c4700e6)

![image](https://github.com/user-attachments/assets/ec810d42-5ca1-458b-8580-af80d845ed9f)

![image](https://github.com/user-attachments/assets/3ce031b3-5b57-40ad-8fc9-160aee648730)

MAC-адрес оппонента болтается за VXLAN1-интерфейсом
![image](https://github.com/user-attachments/assets/5c33e54c-a37a-4558-9e05-1ca55b199bc6)

Конфигурации:

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
logging console debugging
!
hostname Spine1
!
spanning-tree mode mstp
!
interface Ethernet1
   no switchport
   ip address 10.1.0.0/31
   no ip ospf neighbor bfd
!
interface Ethernet2
   no switchport
   ip address 10.1.0.2/31
   no ip ospf neighbor bfd
!
interface Ethernet3
   no switchport
   ip address 10.1.0.4/31
   no ip ospf neighbor bfd
!
interface Ethernet4
   no switchport
   ip address 10.1.0.6/31
   no ip ospf neighbor bfd
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
   bgp cluster-id 10.1.1.0
   maximum-paths 10 ecmp 10
   neighbor 10.1.0.1 remote-as 65210
   neighbor 10.1.0.1 next-hop-self
   neighbor 10.1.0.3 remote-as 65211
   neighbor 10.1.0.3 next-hop-self
   neighbor 10.1.0.5 remote-as 65212
   neighbor 10.1.0.5 next-hop-self
   neighbor 10.1.0.7 remote-as 65213
   neighbor 10.1.0.7 next-hop-self
   !
   address-family evpn
      neighbor 10.1.0.1 activate
      neighbor 10.1.0.3 activate
      neighbor 10.1.0.5 activate
      neighbor 10.1.0.7 activate
   !
   address-family ipv4
      neighbor 10.1.0.1 activate
      neighbor 10.1.0.3 activate
      neighbor 10.1.0.5 activate
      neighbor 10.1.0.7 activate
      network 10.1.0.0/31
      network 10.1.0.2/31
      network 10.1.0.4/31
      network 10.1.0.6/31
      network 10.1.1.0/32
      network 10.255.1.0/32
!
end

```

## Spine2

```

Spine2(config)#sh run
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
   no ip ospf neighbor bfd
!
interface Ethernet2
   no switchport
   ip address 10.1.0.10/31
   no ip ospf neighbor bfd
!
interface Ethernet3
   no switchport
   ip address 10.1.0.12/31
   no ip ospf neighbor bfd
!
interface Ethernet4
   no switchport
   ip address 10.1.0.14/31
   no ip ospf neighbor bfd
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
   bgp cluster-id 10.1.1.1
   maximum-paths 10 ecmp 10
   neighbor 10.1.0.9 remote-as 65210
   neighbor 10.1.0.9 next-hop-self
   neighbor 10.1.0.11 remote-as 65211
   neighbor 10.1.0.11 next-hop-self
   neighbor 10.1.0.13 remote-as 65212
   neighbor 10.1.0.13 next-hop-self
   neighbor 10.1.0.15 remote-as 65213
   neighbor 10.1.0.15 next-hop-self
   !
   address-family evpn
      neighbor 10.1.0.9 activate
      neighbor 10.1.0.11 activate
      neighbor 10.1.0.13 activate
      neighbor 10.1.0.15 activate
   !
   address-family ipv4
      neighbor 10.1.0.9 activate
      neighbor 10.1.0.11 activate
      neighbor 10.1.0.13 activate
      neighbor 10.1.0.15 activate
      network 10.1.0.8/31
      network 10.1.0.10/31
      network 10.1.0.12/31
      network 10.1.0.14/31
      network 10.1.1.1/32
      network 10.255.1.1/32
!
end
Spine2(config)#

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
   no ip ospf neighbor bfd
!
interface Ethernet2
   no switchport
   ip address 10.1.0.18/31
   no ip ospf neighbor bfd
!
interface Ethernet3
   no switchport
   ip address 10.1.0.20/31
   no ip ospf neighbor bfd
!
interface Ethernet4
   no switchport
   ip address 10.1.0.22/31
   no ip ospf neighbor bfd
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
   bgp cluster-id 10.1.1.2
   maximum-paths 10 ecmp 10
   neighbor 10.1.0.17 remote-as 65210
   neighbor 10.1.0.17 next-hop-self
   neighbor 10.1.0.19 remote-as 65211
   neighbor 10.1.0.19 next-hop-self
   neighbor 10.1.0.21 remote-as 65212
   neighbor 10.1.0.21 next-hop-self
   neighbor 10.1.0.23 remote-as 65213
   neighbor 10.1.0.23 next-hop-self
   !
   address-family evpn
      neighbor 10.1.0.17 activate
      neighbor 10.1.0.19 activate
      neighbor 10.1.0.21 activate
      neighbor 10.1.0.23 activate
   !
   address-family ipv4
      neighbor 10.1.0.17 activate
      neighbor 10.1.0.19 activate
      neighbor 10.1.0.21 activate
      neighbor 10.1.0.23 activate
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
   name CLIENTS
!
vlan 20
   name ADMINS
!
interface Ethernet1
   no switchport
   ip address 10.1.0.1/31
   no ip ospf neighbor bfd
!
interface Ethernet2
   no switchport
   ip address 10.1.0.9/31
   no ip ospf neighbor bfd
!
interface Ethernet3
   no switchport
   ip address 10.1.0.17/31
   no ip ospf neighbor bfd
!
interface Ethernet4
   switchport access vlan 10
   no ip ospf neighbor bfd
!
interface Ethernet5
   switchport access vlan 20
!
interface Ethernet6
!
interface Ethernet7
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
   ip address 172.16.0.3/24
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 10100
   vxlan vlan 20 vni 10200
   vxlan flood vtep 10.255.1.11 10.255.1.12 10.255.1.13
!
ip routing
!
route-map RM_Leave_BGP permit 10
   match as 65210
!
router bgp 65210
   router-id 10.1.1.10
   maximum-paths 10 ecmp 10
   neighbor 10.1.0.0 remote-as 65000
   neighbor 10.1.0.8 remote-as 65000
   neighbor 10.1.0.16 remote-as 65000
   !
   address-family evpn
      neighbor 10.1.0.0 activate
      neighbor 10.1.0.8 activate
      neighbor 10.1.0.16 activate
   !
   address-family ipv4
      neighbor 10.1.0.0 activate
      neighbor 10.1.0.8 activate
      neighbor 10.1.0.16 activate
      network 10.1.0.0/31
      network 10.1.0.8/31
      network 10.1.0.16/31
      network 10.1.1.10/32
      network 10.255.1.10/32
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
   name CLIENTS
!
vlan 20
   name ADMINS
!
interface Ethernet1
   no switchport
   ip address 10.1.0.3/31
   no ip ospf neighbor bfd
!
interface Ethernet2
   no switchport
   ip address 10.1.0.11/31
   no ip ospf neighbor bfd
!
interface Ethernet3
   no switchport
   ip address 10.1.0.19/31
   no ip ospf neighbor bfd
!
interface Ethernet4
   switchport access vlan 10
   no ip ospf neighbor bfd
!
interface Ethernet5
   switchport access vlan 20
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
   vxlan flood vtep 10.255.1.10 10.255.1.12 10.255.1.13
!
ip routing
!
router bgp 65211
   router-id 10.1.1.11
   maximum-paths 10 ecmp 10
   neighbor 10.1.0.2 remote-as 65000
   neighbor 10.1.0.10 remote-as 65000
   neighbor 10.1.0.18 remote-as 65000
   !
   address-family evpn
      neighbor 10.1.0.2 activate
      neighbor 10.1.0.10 activate
      neighbor 10.1.0.18 activate
   !
   address-family ipv4
      neighbor 10.1.0.2 activate
      neighbor 10.1.0.10 activate
      neighbor 10.1.0.18 activate
      network 10.1.0.2/31
      network 10.1.0.10/31
      network 10.1.0.18/31
      network 10.1.1.11/32
      network 10.255.1.11/32
!
end
Leaf2#wr

```

## Leaf3

```

Leaf3(config)#
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
   name CLIENTS
!
vlan 20
   name ADMINS
!
interface Ethernet1
   no switchport
   ip address 10.1.0.5/31
   no ip ospf neighbor bfd
!
interface Ethernet2
   no switchport
   ip address 10.1.0.13/31
   no ip ospf neighbor bfd
!
interface Ethernet3
   no switchport
   ip address 10.1.0.21/31
   no ip ospf neighbor bfd
!
interface Ethernet4
   switchport access vlan 10
   no ip ospf neighbor bfd
!
interface Ethernet5
   switchport access vlan 20
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
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
   vxlan flood vtep 10.255.1.10 10.255.1.11 10.255.1.13
!
ip routing
!
router bgp 65212
   router-id 10.1.1.12
   maximum-paths 10 ecmp 10
   neighbor 10.1.0.4 remote-as 65000
   neighbor 10.1.0.12 remote-as 65000
   neighbor 10.1.0.20 remote-as 65000
   !
   address-family evpn
      neighbor 10.1.0.4 activate
      neighbor 10.1.0.12 activate
      neighbor 10.1.0.20 activate
   !
   address-family ipv4
      neighbor 10.1.0.4 activate
      neighbor 10.1.0.12 activate
      neighbor 10.1.0.20 activate
      network 10.1.0.4/31
      network 10.1.0.12/31
      network 10.1.0.20/31
      network 10.1.1.12/32
      network 10.255.1.12/32
!
end
Leaf3# wr

```

## Leaf4

```
Leaf4#sh run
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
   name CLIENTS
!
vlan 20
   name ADMINS
!
interface Ethernet1
   no switchport
   ip address 10.1.0.7/31
   no ip ospf neighbor bfd
!
interface Ethernet2
   no switchport
   ip address 10.1.0.15/31
   no ip ospf neighbor bfd
!
interface Ethernet3
   no switchport
   ip address 10.1.0.23/31
   no ip ospf neighbor bfd
!
interface Ethernet4
   switchport access vlan 10
   no ip ospf neighbor bfd
!
interface Ethernet5
   switchport access vlan 20
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Loopback0
   ip address 10.1.1.13/32
!
interface Loopback1
   ip address 10.255.1.13/32
!
interface Management1
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 10100
   vxlan vlan 20 vni 10200
   vxlan flood vtep 10.255.1.10 10.255.1.11 10.255.1.12
!
ip routing
!
router bgp 65213
   router-id 10.1.1.13
   maximum-paths 10 ecmp 10
   neighbor 10.1.0.6 remote-as 65000
   neighbor 10.1.0.14 remote-as 65000
   neighbor 10.1.0.22 remote-as 65000
   !
   address-family evpn
      neighbor 10.1.0.6 activate
      neighbor 10.1.0.13 activate
      neighbor 10.1.0.22 activate
   !
   address-family ipv4
      neighbor 10.1.0.6 activate
      neighbor 10.1.0.14 activate
      neighbor 10.1.0.22 activate
      network 10.1.0.6/31
      network 10.1.0.14/31
      network 10.1.0.22/31
      network 10.1.1.13/32
      network 10.255.1.13/32
!
end
Leaf4#     wr

```














