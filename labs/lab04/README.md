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

Дошло в чем был неправ, роут-рефлектор нам тут вообще глобально ни к чему, он не про это и не в этой схеме
на любом лифе/спайне есть все маршруты, пингуется каждый интерфейс каждого роутера в стенде. для лифа4 тесты ниже. ну и далее по всем устройствам

![image](https://github.com/user-attachments/assets/4d9c8a72-56b4-4288-ab7d-46f3d2d48cad)

![image](https://github.com/user-attachments/assets/45f74b4a-678a-466e-ad41-44d5118d2be4)


## Spine1

![image](https://github.com/user-attachments/assets/13cffb7c-cdca-4e7e-9384-92b8ef5a8fe7)

## Spine2

![image](https://github.com/user-attachments/assets/7b164b99-db1c-4317-8661-84b14d083676)

## Spine3

![image](https://github.com/user-attachments/assets/9a810cf6-c63e-4ddd-9f00-f5d8dfd52126)

## Leaf 1

![image](https://github.com/user-attachments/assets/aa83d3cd-b98b-48cd-9d85-ec81743ae587)

![image](https://github.com/user-attachments/assets/bccc476a-82ae-4993-b4ac-d3de8c15d4d1)

## Leaf 2

![image](https://github.com/user-attachments/assets/25f6ca7f-ed0b-4460-a84e-2f610698c389)

![image](https://github.com/user-attachments/assets/00e44a27-8d0a-4d6a-9d41-fca758e60540)

## Leaf 3

![image](https://github.com/user-attachments/assets/7cb8bf5c-55e3-42fa-841e-936c9171e7f3)

![image](https://github.com/user-attachments/assets/70bc5b62-6bc5-48ad-8b66-24957574c1c1)

## Leaf 4

![image](https://github.com/user-attachments/assets/19c53501-5d0d-4d61-b400-f188e1e13795)

![image](https://github.com/user-attachments/assets/50f4e079-b2ca-4b9f-9a0a-c0fd58299d91)

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
