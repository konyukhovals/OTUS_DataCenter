
Hometask 2.11 - VxLAN. L2 VNI by Static - Cisco Nexus

# Наша топология

<img width="1512" height="1043" alt="image" src="https://github.com/user-attachments/assets/3e2d2dcb-90d8-4ad9-b81b-048f6954ad1f" />


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

Для демонстрации сделано 2 VxLAN, 10010 И 10020, ассоциированы с VLAN 10 И 20. Сатические VNI между Leaf1 И Leaf3, VLANs 10,20.

<img width="903" height="615" alt="image" src="https://github.com/user-attachments/assets/ea40aca0-ffd1-4961-b39b-99fbf8693b6a" />

<img width="917" height="660" alt="image" src="https://github.com/user-attachments/assets/bf1100ab-b1c5-4398-a8f1-ac390fbb1994" />

<img width="799" height="1048" alt="image" src="https://github.com/user-attachments/assets/ecc355da-491d-4916-9d12-b5c2040e41b2" />

<img width="1115" height="613" alt="image" src="https://github.com/user-attachments/assets/84b73f1c-d601-453f-92d2-0ceb51531b8f" />

<img width="2021" height="1160" alt="изображение" src="https://github.com/user-attachments/assets/93b05a53-ce11-49f4-8294-35be1401edd3" />



## Spine1

```
hostname Spine1

feature bgp
feature pim
feature vn-segment-vlan-based
feature nv overlay

vlan 1

interface Ethernet1/1
  ip address 10.1.0.0/31
  no shutdown

interface Ethernet1/2
  ip address 10.1.0.2/31
  no shutdown

interface Ethernet1/3
  ip address 10.1.0.4/31
  no shutdown

interface loopback0
  description Underlay
  ip address 10.1.1.0/32

interface loopback1
  description Overlay
  ip address 10.255.1.0/32

router bgp 65000
  router-id 10.1.1.0
  address-family ipv4 unicast
    network 10.1.0.0/31
    network 10.1.0.2/31
    network 10.1.0.4/31
    network 10.1.1.0/32
    network 10.255.1.0/32
  neighbor 10.1.0.1
    remote-as 65210
    address-family ipv4 unicast
  neighbor 10.1.0.3
    remote-as 65211
    address-family ipv4 unicast
  neighbor 10.1.0.5
    remote-as 65212
    address-family ipv4 unicast
```

## Spine2

```

hostname Spine2

feature bgp
feature pim
feature vn-segment-vlan-based
feature nv overlay

vlan 1

vrf context management

interface Ethernet1/1
  ip address 10.1.0.6/31
  no shutdown

interface Ethernet1/2
  ip address 10.1.0.8/31
  no shutdown

interface Ethernet1/3
  ip address 10.1.0.10/31
  no shutdown

interface loopback0
  description Underlay
  ip address 10.1.1.1/32

interface loopback1
  description Overlay
  ip address 10.255.1.1/32

router bgp 65000
  router-id 10.1.1.1
  address-family ipv4 unicast
    network 10.1.0.6/31
    network 10.1.0.8/31
    network 10.1.0.10/31
    network 10.1.1.1/32
    network 10.255.1.1/32
  neighbor 10.1.0.7
    remote-as 65210
    address-family ipv4 unicast
  neighbor 10.1.0.9
    remote-as 65211
    address-family ipv4 unicast
  neighbor 10.1.0.11
    remote-as 65212
    address-family ipv4 unicast

```


## Leaf1

```
 
hostname Leaf1

feature bgp
feature pim
feature vn-segment-vlan-based
feature nv overlay

vlan 1,10,20
vlan 10
  name Client10
  vn-segment 10
vlan 20
  name Client20
  vn-segment 20

interface nve1
  no shutdown
  source-interface loopback1
  member vni 10
    ingress-replication protocol static
      peer-ip 10.255.1.12
  member vni 20
    ingress-replication protocol static
      peer-ip 10.255.1.12

interface Ethernet1/1
  ip address 10.1.0.1/31
  no shutdown

interface Ethernet1/2
  ip address 10.1.0.7/31
  no shutdown

interface Ethernet1/6
  description Client20
  switchport
  switchport access vlan 20
  no shutdown

interface Ethernet1/7
  description Client10
  switchport
  switchport access vlan 10
  no shutdown

interface loopback0
  description Underlay
  ip address 10.1.1.10/32

interface loopback1
  description Overderlay
  ip address 10.255.1.10/32

router bgp 65210
  router-id 10.1.1.10
  address-family ipv4 unicast
    network 10.1.0.0/31
    network 10.1.0.6/31
    network 10.1.1.10/32
    network 10.255.1.10/32
  neighbor 10.1.0.0
    remote-as 65000
    address-family ipv4 unicast
  neighbor 10.1.0.6
    remote-as 65000
    address-family ipv4 unicast

```

## Leaf2

```

hostname Leaf2

feature bgp
feature pim
feature vn-segment-vlan-based
feature nv overlay

vlan 1,10,20

vrf context management

interface Ethernet1/1
  ip address 10.1.0.3/31
  no shutdown

interface Ethernet1/2
  ip address 10.1.0.9/31
  no shutdown

interface Ethernet1/6
  description Client20
  switchport
  switchport access vlan 20
  no shutdown

interface Ethernet1/7
  description Client10
  switchport
  switchport access vlan 10
  no shutdown

interface loopback0
  description Underlay
  ip address 10.1.1.11/32

interface loopback1
  description Overderlay
  ip address 10.255.1.11/32

router bgp 65211
  router-id 10.1.1.11
  address-family ipv4 unicast
    network 10.1.0.2/31
    network 10.1.0.8/31
    network 10.1.1.11/32
    network 10.255.1.11/32
  neighbor 10.1.0.2
    remote-as 65000
    address-family ipv4 unicast
  neighbor 10.1.0.8
    remote-as 65000
    address-family ipv4 unicast

```

## Leaf3

```

hostname Leaf3

feature bgp
feature pim
feature vn-segment-vlan-based
feature nv overlay

vlan 1,10,20
vlan 10
  name Client10
  vn-segment 10
vlan 20
  name Client20
  vn-segment 20

interface nve1
  no shutdown
  source-interface loopback1
  member vni 10
    ingress-replication protocol static
      peer-ip 10.255.1.10
  member vni 20
    ingress-replication protocol static
      peer-ip 10.255.1.10

interface Ethernet1/1
  ip address 10.1.0.5/31
  no shutdown

interface Ethernet1/2
  ip address 10.1.0.11/31
  no shutdown

interface Ethernet1/6
  description Client20
  switchport
  switchport access vlan 20
  no shutdown

interface Ethernet1/7
  description Client10
  switchport
  switchport access vlan 10
  no shutdown

interface loopback0
  description Underlay
  ip address 10.1.1.12/32

interface loopback1
  description Overderlay
  ip address 10.255.1.12/32
icam monitor scale

line console
line vty
router bgp 65212
  router-id 10.1.1.12
  address-family ipv4 unicast
    network 10.1.0.4/31
    network 10.1.0.10/31
    network 10.1.1.11/32
    network 10.255.1.12/32
  neighbor 10.1.0.4
    remote-as 65000
    address-family ipv4 unicast
  neighbor 10.1.0.10
    remote-as 65000
    address-family ipv4 unicast

```











