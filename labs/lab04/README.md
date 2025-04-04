Hometask 1.8 - eBGP Underlay

# Наша топология

![image](https://github.com/user-attachments/assets/9db33933-cd81-45fa-bd07-cc0c17f96a35)

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

Схема условно нерабочая, прошу помощи. я не понимаю, что происходит. спайны должны раздавать маршруты - на лифы вроде оно приходит. информация о лупбеках есть не у всех, с лифов нельзя пинговать лупбеки других лифов

![image](https://github.com/user-attachments/assets/312341ef-18d0-4632-a33d-072caf0bd537)


## Spine1

![image](https://github.com/user-attachments/assets/a7d8d1c0-c541-4eeb-810f-2e17f3e92e20)

## Spine2

![image](https://github.com/user-attachments/assets/496a3b7f-5604-4e27-9163-ae798df25c28)

## Spine3

![image](https://github.com/user-attachments/assets/555a5439-7382-49e3-89e1-c481c6b6625d)

## Leaf 1

![image](https://github.com/user-attachments/assets/97a6f364-25a2-486f-8260-d96632c5a12b)

## Leaf 2

![image](https://github.com/user-attachments/assets/56e606f6-2fad-4cfb-8a2f-7053aff68b05)

## Leaf 3

![image](https://github.com/user-attachments/assets/949879c5-e17e-4238-bb84-a9576328ed32)

## Leaf 4

![image](https://github.com/user-attachments/assets/9bbccec3-924b-494e-9cba-32fa1da4b11b)

# Конфигурации устройств

## Spine1

```
sh run
! Command: show running-config
! device: Spine1 (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model ribd
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
inter
        neighbor 10.1.0.3 next-hop-self
   neighbor 10.1.0.3 route-reflector-client
   neighbor 10.1.0.5 remote-as 65212
   neighbor 10.1.0.5 next-hop-self
   neighbor 10.1.0.5 route-reflector-client
   neighbor 10.1.0.7 remote-as 65213
   neighbor 10.1.0.7 next-hop-self
   neighbor 10.1.0.7 route-reflector-client
   !
   address-family ipv4
      neighbor 10.1.0.1 activate
      neighbor 10.1.0.3 activate
      neighbor 10.1.0.5 activate
      neighbor 10.1.0.7 activate
      network 10.1.1.0/32
!
end

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
service routing protocols model ribd
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
   neighbor 10.1.0.9 route-reflector-client
   neighbor 10.1.0.11 remote-as 65211
   neighbor 10.1.0.11 next-hop-self
   neighbor 10.1.0.11 route-reflector-client
   neighbor 10.1.0.13 remote-as 65212
   neighbor 10.1.0.13 next-hop-self
   neighbor 10.1.0.13 route-reflector-client
   neighbor 10.1.0.15 remote-as 65213
   neighbor 10.1.0.15 next-hop-self
   neighbor 10.1.0.15 route-reflector-client
   !
   address-family ipv4
      neighbor 10.1.0.9 activate
      neighbor 10.1.0.11 activate
      neighbor 10.1.0.13 activate
      neighbor 10.1.0.15 activate
      network 10.1.1.1/32
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
service routing protocols model ribd
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
   neighbor 10.1.0.17 route-reflector-client
   neighbor 10.1.0.19 remote-as 65211
   neighbor 10.1.0.19 next-hop-self
   neighbor 10.1.0.19 route-reflector-client
   neighbor 10.1.0.21 remote-as 65212
   neighbor 10.1.0.21 next-hop-self
   neighbor 10.1.0.21 route-reflector-client
   neighbor 10.1.0.23 remote-as 65213
   neighbor 10.1.0.23 next-hop-self
   neighbor 10.1.0.23 route-reflector-client
   !
   address-family ipv4
      neighbor 10.1.0.17 activate
      neighbor 10.1.0.19 activate
      neighbor 10.1.0.21 activate
      neighbor 10.1.0.23 activate
      network 10.1.1.2/32
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
service routing protocols model ribd
!
hostname Leaf1
!
spanning-tree mode mstp
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
   ip address 10.1.1.10/32
!
interface Loopback1
   ip address 10.255.1.10/32
!
interface Management1
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
   address-family ipv4
      neighbor 10.1.0.0 activate
      neighbor 10.1.0.8 activate
      neighbor 10.1.0.16 activate
      network 10.1.1.10/32
!
end

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
service routing protocols model ribd
!
hostname Leaf2
!
spanning-tree mode mstp
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
   ip address 10.1.1.11/32
!
interface Loopback1
   ip address 10.255.1.11/32
!
interface Management1
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
   address-family ipv4
      neighbor 10.1.0.2 activate
      neighbor 10.1.0.10 activate
      neighbor 10.1.0.18 activate
      network 10.1.1.11/32
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
service routing protocols model ribd
!
hostname Leaf3
!
spanning-tree mode mstp
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
   ip address 10.1.1.12/32
!
interface Loopback1
   ip address 10.255.1.12/32
!
interface Management1
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
   address-family ipv4
      neighbor 10.1.0.4 activate
      neighbor 10.1.0.12 activate
      neighbor 10.1.0.20 activate
      network 10.1.1.12/32
!
end

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
service routing protocols model ribd
!
hostname Leaf4
!
spanning-tree mode mstp
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
   ip address 10.1.1.13/32
!
interface Loopback1
   ip address 10.255.1.13/32
!
interface Management1
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
   address-family ipv4
      neighbor 10.1.0.6 activate
      neighbor 10.1.0.14 activate
      neighbor 10.1.0.22 activate
      network 10.1.1.13/32
!
end

```
