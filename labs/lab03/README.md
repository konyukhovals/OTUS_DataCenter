Hometask 1.5

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


# Демонстрация работы схемы

## Spine1

![image](https://github.com/user-attachments/assets/5755d735-a720-41c7-8112-fe1eb1cc28be)

## Spine2

![image](https://github.com/user-attachments/assets/c1af3967-c58e-468d-8954-52f10ce2302d)

## Spine3

![image](https://github.com/user-attachments/assets/9438bd80-9122-4f06-82ad-7b8cfc286dbe)

## Leaf 1

![image](https://github.com/user-attachments/assets/b8d3f911-a357-46b2-808e-1223af325cf5)

## Leaf 2

![image](https://github.com/user-attachments/assets/88ea5020-c1b2-43f9-bd3e-0131caa05c51)

## Leaf 3

![image](https://github.com/user-attachments/assets/0ef82083-2b4b-48cb-902e-a24e1e39a33f)

## Leaf 4

![image](https://github.com/user-attachments/assets/b0a4469c-b3b5-4ae5-bb6c-078a5f93e1ae)

# Конфигурации устройств

## Spine1

```
Spine1#
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
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
!
interface Ethernet2
   no switchport
   ip address 10.1.0.2/31
   no ip ospf neighbor bfd
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
!
interface Ethernet3
   no switchport
   ip address 10.1.0.4/31
   no ip ospf neighbor bfd
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
!
interface Ethernet4
   no switchport
   ip address 10.1.0.6/31
   no ip ospf neighbor bfd
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
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
   isis enable UNDERLAY
   isis circuit-type level-2
!
interface Loopback1
   ip address 10.255.1.0/32
!
interface Management1
!
ip routing
!
router isis UNDERLAY
   net 49.0001.0000.0000.0101.00
   is-type level-2
   log-adjacency-changes
   !
   address-family ipv4 unicast
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
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
!
interface Ethernet2
   no switchport
   ip address 10.1.0.10/31
   no ip ospf neighbor bfd
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
!
interface Ethernet3
   no switchport
   ip address 10.1.0.12/31
   no ip ospf neighbor bfd
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
!
interface Ethernet4
   no switchport
   ip address 10.1.0.14/31
   no ip ospf neighbor bfd
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
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
   isis enable UNDERLAY
   isis circuit-type level-2
!
interface Loopback1
   ip address 10.255.1.1/32
!
interface Management1
!
ip routing
!
router isis UNDERLAY
   net 49.0001.0000.0000.0102.00
   is-type level-2
   log-adjacency-changes
   !
   address-family ipv4 unicast
!
end

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
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
!
interface Ethernet2
   no switchport
   ip address 10.1.0.18/31
   no ip ospf neighbor bfd
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
!
interface Ethernet3
   no switchport
   ip address 10.1.0.20/31
   no ip ospf neighbor bfd
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
!
interface Ethernet4
   no switchport
   ip address 10.1.0.22/31
   no ip ospf neighbor bfd
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
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
   isis enable UNDERLAY
   isis circuit-type level-2
!
interface Loopback1
   ip address 10.255.1.2/32
!
interface Management1
!
ip routing
!
router isis UNDERLAY
   net 49.0001.0000.0000.0103.00
   is-type level-2
   log-adjacency-changes
   !
   address-family ipv4 unicast
!
end
Spine3#

```

## Leaf 1

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
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
!
interface Ethernet2
   no switchport
   ip address 10.1.0.9/31
   no ip ospf neighbor bfd
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
!
interface Ethernet3
   no switchport
   ip address 10.1.0.17/31
   no ip ospf neighbor bfd
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
!
interface Ethernet4
   no ip ospf neighbor bfd
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
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
   isis enable UNDERLAY
   isis circuit-type level-2
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
router isis UNDERLAY
   net 49.0001.0000.0000.0111.00
   is-type level-2
   log-adjacency-changes
   !
   address-family ipv4 unicast
!
end
Leaf1#

```

## Leaf 2

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
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
!
interface Ethernet2
   no switchport
   ip address 10.1.0.11/31
   no ip ospf neighbor bfd
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
!
interface Ethernet3
   no switchport
   ip address 10.1.0.19/31
   no ip ospf neighbor bfd
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
!
interface Ethernet4
   no ip ospf neighbor bfd
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
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
   isis enable UNDERLAY
   isis circuit-type level-2
!
interface Loopback1
   ip address 10.255.1.11/32
!
interface Management1
!
ip routing
!
router isis UNDERLAY
   net 49.0001.0000.0000.0112.00
   is-type level-2
   log-adjacency-changes
   !
   address-family ipv4 unicast
!
end
Leaf2#

```

## Leaf 3

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
   no ip ospf neighbor bfd
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
!
interface Ethernet2
   no switchport
   ip address 10.1.0.13/31
   no ip ospf neighbor bfd
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
!
interface Ethernet3
   no switchport
   ip address 10.1.0.21/31
   no ip ospf neighbor bfd
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
!
interface Ethernet4
   no ip ospf neighbor bfd
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
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
   isis enable UNDERLAY
   isis circuit-type level-2
!
interface Loopback1
   ip address 10.255.1.12/32
!
interface Management1
!
ip routing
!
router isis UNDERLAY
   net 49.0001.0000.0000.0113.00
   is-type level-2
   log-adjacency-changes
   !
   address-family ipv4 unicast
!
end
Leaf3#

```

## Leaf 4

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
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
!
interface Ethernet2
   no switchport
   ip address 10.1.0.15/31
   no ip ospf neighbor bfd
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
!
interface Ethernet3
   no switchport
   ip address 10.1.0.23/31
   no ip ospf neighbor bfd
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
!
interface Ethernet4
   no ip ospf neighbor bfd
   isis enable UNDERLAY
   isis circuit-type level-2
   isis network point-to-point
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
   isis enable UNDERLAY
   isis circuit-type level-2
!
interface Loopback1
   ip address 10.255.1.13/32
!
interface Management1
!
ip routing
!
router isis UNDERLAY
   net 49.0001.0000.0000.0114.00
   is-type level-2
   log-adjacency-changes
   !
   address-family ipv4 unicast
!
end
Leaf4#

```







