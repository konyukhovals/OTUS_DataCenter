Hometask 1.4

# Наша топология

![image](https://github.com/user-attachments/assets/ccc4513e-5f6f-4a68-8e23-33e4cac4263d)

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


# Конфиги устройств

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
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 bC/+yh1oWDr/xmAf7jOVNAilE9fhduti
!
interface Ethernet2
   no switchport
   ip address 10.1.0.2/31
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 0xRoP+PdkvrxqP8xKzY+YLBzjO1vmteZ
!
interface Ethernet3
   no switchport
   ip address 10.1.0.4/31
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 0xRoP+PdkvrxqP8xKzY+YLBzjO1vmteZ
!
interface Ethernet4
   no switchport
   ip address 10.1.0.6/31
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 0xRoP+PdkvrxqP8xKzY+YLBzjO1vmteZ
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
   ip ospf area 0.0.0.0
!
interface Loopback1
   ip address 10.255.1.0/32
!
interface Management1
!
ip routing
!
router ospf 111
   router-id 10.1.1.0
   auto-cost reference-bandwidth 100000
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet3
   no passive-interface Ethernet4
   max-lsa 12000
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
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 bC/+yh1oWDr/xmAf7jOVNAilE9fhduti
!
interface Ethernet2
   no switchport
   ip address 10.1.0.10/31
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 0xRoP+PdkvrxqP8xKzY+YLBzjO1vmteZ
!
interface Ethernet3
   no switchport
   ip address 10.1.0.12/31
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 0xRoP+PdkvrxqP8xKzY+YLBzjO1vmteZ
!
interface Ethernet4
   no switchport
   ip address 10.1.0.14/31
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 0xRoP+PdkvrxqP8xKzY+YLBzjO1vmteZ
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
   ip ospf area 0.0.0.0
!
interface Loopback1
   ip address 10.255.1.1/32
!
interface Management1
!
ip routing
!
router ospf 111
   router-id 10.1.1.1
   auto-cost reference-bandwidth 100000
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet3
   no passive-interface Ethernet4
   max-lsa 12000
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
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 bC/+yh1oWDr/xmAf7jOVNAilE9fhduti
!
interface Ethernet2
   no switchport
   ip address 10.1.0.18/31
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 0xRoP+PdkvrxqP8xKzY+YLBzjO1vmteZ
!
interface Ethernet3
   no switchport
   ip address 10.1.0.20/31
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 0xRoP+PdkvrxqP8xKzY+YLBzjO1vmteZ
!
interface Ethernet4
   no switchport
   ip address 10.1.0.22/31
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 0xRoP+PdkvrxqP8xKzY+YLBzjO1vmteZ
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
   ip ospf area 0.0.0.0
!
interface Loopback1
   ip address 10.255.1.2/32
!
interface Management1
!
ip routing
!
router ospf 111
   router-id 10.1.1.2
   auto-cost reference-bandwidth 100000
   bfd default
   bfd adjacency state any
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet3
   no passive-interface Ethernet4
   max-lsa 12000
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
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 bC/+yh1oWDr/xmAf7jOVNAilE9fhduti
!
interface Ethernet2
   no switchport
   ip address 10.1.0.9/31
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 0xRoP+PdkvrxqP8xKzY+YLBzjO1vmteZ
!
interface Ethernet3
   no switchport
   ip address 10.1.0.17/31
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 0xRoP+PdkvrxqP8xKzY+YLBzjO1vmteZ
!
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
   ip ospf area 0.0.0.0
!
interface Loopback1
   ip address 10.255.1.10/32
!
interface Management1
!
ip routing
!
router bgp 100
   neighbor 10.0.0.2 remote-as 200
   !
   address-family ipv4
      neighbor 10.0.0.2 activate
!
router ospf 111
   router-id 10.1.1.10
   auto-cost reference-bandwidth 100000
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet3
   max-lsa 12000
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
service routing protocols model ribd
!
hostname Leaf2
!
spanning-tree mode mstp
!
interface Ethernet1
   no switchport
   ip address 10.1.0.3/31
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 bC/+yh1oWDr/xmAf7jOVNAilE9fhduti
!
interface Ethernet2
   no switchport
   ip address 10.1.0.11/31
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 0xRoP+PdkvrxqP8xKzY+YLBzjO1vmteZ
!
interface Ethernet3
   no switchport
   ip address 10.1.0.19/31
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 0xRoP+PdkvrxqP8xKzY+YLBzjO1vmteZ
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
   ip ospf area 0.0.0.0
!
interface Loopback1
   ip address 10.255.1.11/32
!
interface Management1
!
ip routing
!
router ospf 111
   router-id 10.1.1.11
   auto-cost reference-bandwidth 100000
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet3

   max-lsa 12000
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
service routing protocols model ribd
!
hostname Leaf3
!
spanning-tree mode mstp
!
interface Ethernet1
   no switchport
   ip address 10.1.0.5/31
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 bC/+yh1oWDr/xmAf7jOVNAilE9fhduti
!
interface Ethernet2
   no switchport
   ip address 10.1.0.13/31
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 0xRoP+PdkvrxqP8xKzY+YLBzjO1vmteZ
!
interface Ethernet3
   no switchport
   ip address 10.1.0.21/31
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 0xRoP+PdkvrxqP8xKzY+YLBzjO1vmteZ

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
   ip ospf area 0.0.0.0
!
interface Loopback1
   ip address 10.255.1.12/32
!
interface Management1
!
ip routing
!
router ospf 111
   router-id 10.1.1.12
   auto-cost reference-bandwidth 100000
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet3

   max-lsa 12000
!
end
Leaf3#

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
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 bC/+yh1oWDr/xmAf7jOVNAilE9fhduti
!
interface Ethernet2
   no switchport
   ip address 10.1.0.15/31
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 0xRoP+PdkvrxqP8xKzY+YLBzjO1vmteZ
!
interface Ethernet3
   no switchport
   ip address 10.1.0.23/31
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 0xRoP+PdkvrxqP8xKzY+YLBzjO1vmteZ

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
   ip ospf area 0.0.0.0
!
interface Loopback1
   ip address 10.255.1.13/32
!
interface Management1
!
ip routing
!
router ospf 111
   router-id 10.1.1.13
   auto-cost reference-bandwidth 100000
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet3

   max-lsa 12000
!
end

```

##  Выгрузки статусов работоспособности Спайнов

Spine1

![image](https://github.com/user-attachments/assets/dbf87d13-5c94-4b96-bb85-2ab401e55c55)

Spine2

![image](https://github.com/user-attachments/assets/e7616898-1dd3-44b6-834a-a4ec16e7f053)

Spine3

![image](https://github.com/user-attachments/assets/695c5145-1d47-452a-ba0c-9d2b3a4aab8f)

##  Выгрузки статусов работоспособности Лифов

![image](https://github.com/user-attachments/assets/bc42ae5e-3ca5-4a04-b261-a3ae2d379d83)

![image](https://github.com/user-attachments/assets/c8bec901-ad55-4375-be98-21df3da97608)

![image](https://github.com/user-attachments/assets/8e0163b0-f2aa-4f16-8c08-376af2dc8b26)

![image](https://github.com/user-attachments/assets/4e1981f0-2fda-42d6-aa75-d58951f84eea)






