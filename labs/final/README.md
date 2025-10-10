## Диплом















Spine 1
```
conf t
 hostname Spine1_2

 feature ospf

 interface loopback0
  ip address 10.1.2.0/32
 interface loopback1
  ip address 10.255.2.0/32

 interface Ethernet1/1      ! -> Leaf1_2 E1/1
  description P2P_to_Leaf1_2_E1/1
  ip address 10.1.0.0/31
  no shutdown
 interface Ethernet1/2      ! -> Leaf2_2 E1/1
  description P2P_to_Leaf2_2_E1/1
  ip address 10.1.0.4/31
  no shutdown
 interface Ethernet1/3      ! -> Leaf3_2 E1/1
  description P2P_to_Leaf3_2_E1/1
  ip address 10.1.0.8/31
  no shutdown

 router ospf 10
  router-id 10.1.2.0
  passive-interface default
  no passive-interface Ethernet1/1
  no passive-interface Ethernet1/2
  no passive-interface Ethernet1/3
  network 10.1.2.0/32 area 0.0.0.0
  network 10.1.0.0/31 area 0.0.0.0
  network 10.1.0.4/31 area 0.0.0.0
  network 10.1.0.8/31 area 0.0.0.0
exit
copy run start
```


Spine2
```
conf t
 hostname Spine2_2

 feature ospf

 interface loopback0
  ip address 10.1.2.1/32
 interface loopback1
  ip address 10.255.2.1/32

 interface Ethernet1/1      ! -> Leaf1_2 E1/2
  description P2P_to_Leaf1_2_E1/2
  ip address 10.1.0.2/31
  no shutdown
 interface Ethernet1/2      ! -> Leaf2_2 E1/2
  description P2P_to_Leaf2_2_E1/2
  ip address 10.1.0.6/31
  no shutdown
 interface Ethernet1/3      ! -> Leaf3_2 E1/2
  description P2P_to_Leaf3_2_E1/2
  ip address 10.1.0.10/31
  no shutdown

 router ospf 10
  router-id 10.1.2.1
  passive-interface default
  no passive-interface Ethernet1/1
  no passive-interface Ethernet1/2
  no passive-interface Ethernet1/3
  network 10.1.2.1/32 area 0.0.0.0
  network 10.1.0.2/31 area 0.0.0.0
  network 10.1.0.6/31 area 0.0.0.0
  network 10.1.0.10/31 area 0.0.0.0
exit
copy run start
```


Leaf1
```
conf t
 hostname Leaf1_2

 feature ospf

 interface loopback0
  ip address 10.1.2.10/32
 interface loopback1
  ip address 10.255.2.10/32

 interface Ethernet1/1      ! -> Spine1_2 E1/1
  description P2P_to_Spine1_2_E1/1
  ip address 10.1.0.1/31
  no shutdown
 interface Ethernet1/2      ! -> Spine2_2 E1/1
  description P2P_to_Spine2_2_E1/1
  ip address 10.1.0.3/31
  no shutdown

 router ospf 10
  router-id 10.1.2.10
  passive-interface default
  no passive-interface Ethernet1/1
  no passive-interface Ethernet1/2
  network 10.1.2.10/32 area 0.0.0.0
  network 10.1.0.1/31 area 0.0.0.0
  network 10.1.0.3/31 area 0.0.0.0
exit
copy run start
```

leaf2
```
conf t
 hostname Leaf2_2

 feature ospf

 interface loopback0
  ip address 10.1.2.11/32
 interface loopback1
  ip address 10.255.2.11/32

 interface Ethernet1/1      ! -> Spine1_2 E1/2
  description P2P_to_Spine1_2_E1/2
  ip address 10.1.0.5/31
  no shutdown
 interface Ethernet1/2      ! -> Spine2_2 E1/2
  description P2P_to_Spine2_2_E1/2
  ip address 10.1.0.7/31
  no shutdown

 router ospf 10
  router-id 10.1.2.11
  passive-interface default
  no passive-interface Ethernet1/1
  no passive-interface Ethernet1/2
  network 10.1.2.11/32 area 0.0.0.0
  network 10.1.0.5/31 area 0.0.0.0
  network 10.1.0.7/31 area 0.0.0.0
exit
copy run start
```


leaf3
```
conf t
 hostname Leaf3_2

 feature ospf

 interface loopback0
  ip address 10.1.2.12/32
 interface loopback1
  ip address 10.255.2.12/32

 interface Ethernet1/1      ! -> Spine1_2 E1/3
  description P2P_to_Spine1_2_E1/3
  ip address 10.1.0.9/31
  no shutdown
 interface Ethernet1/2      ! -> Spine2_2 E1/3
  description P2P_to_Spine2_2_E1/3
  ip address 10.1.0.11/31
  no shutdown

 router ospf 10
  router-id 10.1.2.12
  passive-interface default
  no passive-interface Ethernet1/1
  no passive-interface Ethernet1/2
  network 10.1.2.12/32 area 0.0.0.0
  network 10.1.0.9/31 area 0.0.0.0
  network 10.1.0.11/31 area 0.0.0.0
exit
copy run start
```


