Hometask 2.11 - VxLAN. L2 VNI by Flood and Learn - Cisco Nexus

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

Для демонстрации сделано 2 VxLAN, 10010 И 10020, ассоциированы с VLAN 10 И 20. Мультикастный адрес 239.1.1.1

<img width="845" height="540" alt="image" src="https://github.com/user-attachments/assets/275224e7-1bfa-4b63-b03d-237883af4f2d" />

<img width="859" height="495" alt="image" src="https://github.com/user-attachments/assets/8113b4a0-b325-47b5-bece-1f9d4ad40f63" />

<img width="2150" height="1155" alt="image" src="https://github.com/user-attachments/assets/baa8bd36-9324-4de5-a932-844f897b6596" />

На Leaf 1 и 3, где присутствуют клиенты VxLan
<img width="404" height="169" alt="image" src="https://github.com/user-attachments/assets/b2341413-66a8-47cb-871e-10fafa66dbbd" />

На Leaf2 - там пусто

Проверка PIM на Spine1
<img width="1610" height="968" alt="image" src="https://github.com/user-attachments/assets/fe3e8143-af01-415f-8313-36d8a60437ab" />

Mroute на Leaf1
<img width="1004" height="971" alt="image" src="https://github.com/user-attachments/assets/e81c7171-e953-4fe7-8f7c-49637f273ff6" />

<img width="1365" height="1254" alt="image" src="https://github.com/user-attachments/assets/a528cf25-32b6-4596-b1ae-a043d59e0a78" />


## Spine1

```

Spine1#sh run
feature bgp
feature pim
feature vn-segment-vlan-based
feature nv overlay

ip pim rp-address 10.255.1.0 group-list 239.0.0.0/8
ip pim rp-address 10.255.1.1 group-list 239.0.0.0/8

interface Ethernet1/1
  ip address 10.1.0.0/31
  ip pim sparse-mode
  no shutdown

interface Ethernet1/2
  ip address 10.1.0.2/31
  ip pim sparse-mode
  no shutdown

interface Ethernet1/3
  ip address 10.1.0.4/31
  ip pim sparse-mode
  no shutdown

interface loopback0
  description Underlay
  ip address 10.1.1.0/32
  ip pim sparse-mode

interface loopback1
  description Overlay
  ip address 10.255.1.0/32
  ip pim sparse-mode
icam monitor scale

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

Spine2#sh run
feature bgp
feature pim
feature vn-segment-vlan-based
feature nv overlay
ip pim rp-address 10.255.1.0 group-list 239.0.0.0/8
ip pim rp-address 10.255.1.1 group-list 239.0.0.0/8

interface Ethernet1/1
  ip address 10.1.0.6/31
  ip pim sparse-mode
  no shutdown

interface Ethernet1/2
  ip address 10.1.0.8/31
  ip pim sparse-mode
  no shutdown

interface Ethernet1/3
  ip address 10.1.0.10/31
  ip pim sparse-mode
  no shutdown

interface loopback0
  description Underlay
  ip address 10.1.1.1/32
  ip pim sparse-mode

interface loopback1
  description Overlay
  ip address 10.255.1.1/32
  ip pim sparse-mode
icam monitor scale

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
Leaf1#sh run

feature bgp
feature pim
feature vn-segment-vlan-based
feature nv overlay

ip pim rp-address 10.255.1.0 group-list 239.0.0.0/8
ip pim rp-address 10.255.1.1 group-list 239.0.0.0/8

vlan 1,10,20
vlan 10
  name Client10
  vn-segment 10010
vlan 20
  name Client20
  vn-segment 10020

interface nve1
  no shutdown
  source-interface loopback1
  member vni 10010
    mcast-group 239.1.1.1
  member vni 10020
    mcast-group 239.1.1.2

interface Ethernet1/1
  ip address 10.1.0.1/31
  ip pim sparse-mode
  no shutdown

interface Ethernet1/2
  ip address 10.1.0.7/31
  ip pim sparse-mode
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
  ip pim sparse-mode

interface loopback1
  description Overderlay
  ip address 10.255.1.10/32
  ip pim sparse-mode
icam monitor scale

line console
line vty
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
Leaf2#sh run

feature bgp
feature pim
feature vn-segment-vlan-based
feature nv overlay

ip pim rp-address 10.255.1.0 group-list 239.0.0.0/8
ip pim rp-address 10.255.1.1 group-list 239.0.0.0/8

interface Ethernet1/1
  ip address 10.1.0.3/31
  ip pim sparse-mode
  no shutdown

interface Ethernet1/2
  ip address 10.1.0.9/31
  ip pim sparse-mode
  no shutdown

interface loopback0
  description Underlay
  ip address 10.1.1.11/32
  ip pim sparse-mode

interface loopback1
  description Overlay
  ip address 10.255.1.11/32
  ip pim sparse-mode

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

 Leaf3#sh run

feature bgp
feature pim
feature vn-segment-vlan-based
feature nv overlay

ip pim rp-address 10.255.1.0 group-list 239.0.0.0/8
ip pim rp-address 10.255.1.1 group-list 239.0.0.0/8
vlan 1,10,20
vlan 10
  name Client10
  vn-segment 10010
vlan 20
  name Client20
  vn-segment 10020

interface nve1
  no shutdown
  source-interface loopback1
  member vni 10010
    mcast-group 239.1.1.1
  member vni 10020
    mcast-group 239.1.1.2

interface Ethernet1/1
  ip address 10.1.0.5/31
  ip pim sparse-mode
  no shutdown

interface Ethernet1/2
  ip address 10.1.0.11/31
  ip pim sparse-mode
  no shutdown

interface Ethernet1/6
  switchport
  switchport access vlan 20
  no shutdown

interface Ethernet1/7
  switchport
  switchport access vlan 10
  no shutdown
  
  
interface loopback0
  description Underlay
  ip address 10.1.1.12/32
  ip pim sparse-mode

interface loopback1
  description Overlay
  ip address 10.255.1.12/32
  ip pim sparse-mode
icam monitor scale

line console
line vty
router bgp 65212
  router-id 10.1.1.12
  address-family ipv4 unicast
    network 10.1.0.4/31
    network 10.1.0.10/31
    network 10.1.1.12/32
    network 10.255.1.12/32
  neighbor 10.1.0.4
    remote-as 65000
    address-family ipv4 unicast
  neighbor 10.1.0.10
    remote-as 65000
    address-family ipv4 unicast


```











