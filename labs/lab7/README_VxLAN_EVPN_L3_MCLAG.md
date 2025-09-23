
Hometask 2.14 - VxLAN EVPN L3 + MC-LAG

# Наша топология

<img width="1589" height="901" alt="image" src="https://github.com/user-attachments/assets/eb404939-2bba-4818-b137-7a87e6f9d257" />

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
Spine1#
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
   maximum-paths 4
   neighbor 10.1.0.1 remote-as 65210
   neighbor 10.1.0.3 remote-as 65211
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
   !
   address-family evpn
      neighbor 10.255.1.10 activate
      neighbor 10.255.1.11 activate
      neighbor 10.255.1.12 activate
   !
   address-family ipv4
      neighbor 10.1.0.1 activate
      neighbor 10.1.0.3 activate
      neighbor 10.1.0.5 activate
      network 10.1.0.0/31
      network 10.1.0.2/31
      network 10.1.0.4/31
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
   ip address 10.1.0.6/31
!
interface Ethernet2
   no switchport
   ip address 10.1.0.8/31
!
interface Ethernet3
   no switchport
   ip address 10.1.0.10/31
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
   maximum-paths 4
   neighbor 10.1.0.7 remote-as 65210
   neighbor 10.1.0.9 remote-as 65211
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
   !
   address-family evpn
      neighbor 10.255.1.10 activate
      neighbor 10.255.1.11 activate
      neighbor 10.255.1.12 activate
   !
   address-family ipv4
      neighbor 10.1.0.7 activate
      neighbor 10.1.0.9 activate
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
Leaf1#
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
vlan 10,20
!
vrf instance TENANT-A
!
interface Ethernet1
   no switchport
   ip address 10.1.0.1/31
!
interface Ethernet2
   no switchport
   ip address 10.1.0.7/31
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
   spanning-tree portfast
!
interface Ethernet8
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
   vxlan vlan 10 vni 10100
   vxlan vlan 20 vni 10200
   vxlan vrf TENANT-A vni 50000
!
ip virtual-router mac-address 00:01:00:01:00:01
!
ip routing
ip routing vrf TENANT-A
!
ip route vrf TENANT-A 192.168.10.0/24 Null0
ip route vrf TENANT-A 192.168.20.0/24 Null0
!
router bgp 65210
   router-id 10.1.1.10
   rd auto
   maximum-paths 4
   neighbor 10.1.0.0 remote-as 65000
   neighbor 10.1.0.6 remote-as 65000
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
      rd auto
      route-target both 10100:10100
      redistribute learned
   !
   vlan 20
      rd auto
      route-target both 10200:10200
      redistribute learned
   !
   address-family evpn
      neighbor 10.255.1.0 activate
      neighbor 10.255.1.1 activate
   !
   address-family ipv4
      neighbor 10.1.0.0 activate
      neighbor 10.1.0.6 activate
      network 10.1.0.0/31
      network 10.1.0.6/31
      network 10.1.1.10/32
      network 10.255.1.10/32
   !
   vrf TENANT-A
      rd 10.255.1.10:50000
      route-target import 50000:50000
      route-target export 50000:50000
      redistribute connected
!
end
Leaf1#
```

## Leaf2

```
Leaf2#
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
vlan 10,20
!
vrf instance TENANT-A
!
interface Ethernet1
   no switchport
   ip address 10.1.0.3/31
!
interface Ethernet2
   no switchport
   ip address 10.1.0.9/31
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
   vxlan vlan 10 vni 10100
   vxlan vlan 20 vni 10200
   vxlan vrf TENANT-A vni 50000
!
ip virtual-router mac-address 00:01:00:01:00:01
!
ip routing
ip routing vrf TENANT-A
!
ip route vrf TENANT-A 192.168.10.0/24 Null0
ip route vrf TENANT-A 192.168.20.0/24 Null0
!
router bgp 65211
   router-id 10.1.1.11
   rd auto
   maximum-paths 4
   neighbor 10.1.0.2 remote-as 65000
   neighbor 10.1.0.8 remote-as 65000
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
      rd auto
      route-target both 10100:10100
      redistribute learned
   !
   vlan 20
      rd auto
      route-target both 10200:10200
      redistribute learned
   !
   address-family evpn
      neighbor 10.255.1.0 activate
      neighbor 10.255.1.1 activate
   !
   address-family ipv4
      neighbor 10.1.0.2 activate
      neighbor 10.1.0.8 activate
      network 10.1.0.2/31
      network 10.1.0.8/31
      network 10.1.1.11/32
      network 10.255.1.11/32
   !
   vrf TENANT-A
      rd 10.255.1.11:50000
      route-target import 50000:50000
      route-target export 50000:50000
      redistribute connected
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
service routing protocols model multi-agent
!
hostname Leaf3
!
spanning-tree mode mstp
!
vlan 10,20
!
vrf instance TENANT-A
!
interface Ethernet1
   no switchport
   ip address 10.1.0.5/31
!
interface Ethernet2
   no switchport
   ip address 10.1.0.11/31
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
   spanning-tree portfast
!
interface Ethernet8
   switchport access vlan 20
   spanning-tree portfast
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
   vxlan vlan 10 vni 10100
   vxlan vlan 20 vni 10200
   vxlan vrf TENANT-A vni 50000
!
ip virtual-router mac-address 00:01:00:01:00:01
!
ip routing
ip routing vrf TENANT-A
!
ip route vrf TENANT-A 192.168.10.0/24 Null0
ip route vrf TENANT-A 192.168.20.0/24 Null0
!
router bgp 65212
   router-id 10.1.1.12
   rd auto
   maximum-paths 4
   neighbor 10.1.0.4 remote-as 65000
   neighbor 10.1.0.10 remote-as 65000
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
      rd auto
      route-target both 10100:10100
      redistribute learned
   !
   vlan 20
      rd auto
      route-target both 10200:10200
      redistribute learned
   !
   address-family evpn
      neighbor 10.255.1.0 activate
      neighbor 10.255.1.1 activate
   !
   address-family ipv4
      neighbor 10.1.0.4 activate
      neighbor 10.1.0.10 activate
      network 10.1.0.4/31
      network 10.1.0.10/31
      network 10.1.1.12/32
      network 10.255.1.12/32
   !
   vrf TENANT-A
      rd 10.255.1.12:50000
      route-target import 50000:50000
      route-target export 50000:50000
      redistribute connected
!
end
Leaf3#
```

# Механика

---

# 1. Underlay

* У нас классическая фабрика spine–leaf.
* Поднята IP-маршрутизация (eBGP в твоём случае).
* Все loopback’и (`Lo0` для underlay, `Lo1` для overlay VTEP) друг другу видны.
* Это просто транспорт для туннелей VXLAN.

---

# 2. Overlay (EVPN)

Overlay = **распределённый L2/L3 по BGP EVPN**.

В BGP EVPN есть несколько типов маршрутов:

* **Type-3 (IMET)** — список участников VNI (VTEP-ов). Нужен для BUM (Broadcast, Unknown unicast, Multicast).
* **Type-2 (MAC/IP Advertisement)** — говорит «у меня за этим VTEP есть MAC (и IP, если ARP suppression)».
* **Type-5 (IP Prefix Route)** — нужен для L3VNI: анонс префиксов из VRF.

Когда клиент за Leaf1 говорит «у меня есть MAC/192.168.10.1», Leaf1 анонсирует это в EVPN. Leaf3 получает этот анонс и понимает: чтобы достучаться к 192.168.10.1 → VXLAN-туннель к VTEP Leaf1 (10.255.1.10).

---

# 3. VRF

* Каждый **VRF** = виртуальная маршрутизируемая таблица.
* Мы сделали VRF **TENANT-A**, где у нас есть два префикса:

  * `192.168.10.0/24` (VLAN10 → VNI10100)
  * `192.168.20.0/24` (VLAN20 → VNI10200)
* VRF нужен для **изоляции маршрутов** (каждый «тенант» = свой VRF).
* У VRF есть свой **L3VNI 50000**, через который по EVPN (Type-5) между лифами синхронизируются маршруты.

---

# 4. Anycast Gateway

* На всех лифах один и тот же IP-шлюз (`192.168.10.254` и `192.168.20.254`) и один и тот же виртуальный MAC (`0001.0001.0001`).
* Это значит: клиент в VLAN10 всегда видит «соседа .254» локально на своём Leaf.
* Но фактически этот IP доступен на **каждом Leaf**.
* Благодаря EVPN Type-2 (MAC/IP) и Type-5 (IP Prefix), если пакет нужно отправить в другой VNI или на другой Leaf, он уходит в VXLAN-туннель.

Клиенту не важно, где живёт другой сегмент — Anycast GW есть везде, а лифы «разруливают» через EVPN.

---

# 5. Как идёт трафик

### Сценарий 1: L2 внутри одного VLAN

PC1 (192.168.10.1) → PC2 (192.168.10.2, но за другим Leaf).

1. PC1 делает ARP «кто 192.168.10.2».
2. Leaf1 проверяет EVPN: знает ли IP/MAC? Если да → ARP-suppression (сам отвечает). Если нет → шлёт BUM по VNI10100 (VXLAN multicast через Type-3).
3. Leaf3 получает, отвечает: «192.168.10.2 = MAC xx\:xx\:xx».
4. Теперь Leaf1 знает: MAC/192.168.10.2 → через VTEP 10.255.1.12.
5. VXLAN-туннель установлен, трафик идёт L2 поверх underlay.

---

### Сценарий 2: L3 между VLAN10 и VLAN20

PC1 (192.168.10.1) → PC3 (192.168.20.2).

1. PC1 отправляет пакет на шлюз .254.
2. Leaf1 принимает на SVI Vlan10 (Anycast GW), понимает: пакет в другой VRF-сегмент (192.168.20.0/24).
3. Leaf1 смотрит в VRF TENANT-A и видит по EVPN Type-5, что сеть 192.168.20.0/24 есть за VTEP Leaf3 (10.255.1.12).
4. Leaf1 инкапсулирует пакет в VXLAN с **VNI50000 (L3VNI)** и шлёт его на Leaf3.
5. Leaf3 получает, «выходит» из L3VNI, пересаживает в VNI10200 (VLAN20) и отправляет в порт к PC3.

---

# 6. ARP Suppression

* Когда клиент делает ARP, Leaf локально отвечает, если знает MAC/IP из EVPN Type-2.
* Это экономит broadcast и ускоряет lookup.

---

Шпаргалка
* **Underlay** = транспорт IP (всё видят loopback-и).
* **Overlay** = L2/L3 фабрика на базе BGP EVPN.
* **VRF + L3VNI** = маршрутизация между сегментами через Type-5.
* **Anycast GW** = единый шлюз, но распределённый на каждом Leaf.
* **EVPN RT-2/3/5** = механика синхронизации MAC/IP/Prefix.

---

## Заметки - Что такое IRB

IRB = Integrated Routing and Bridging.
Это модель, где Leaf одновременно:
выполняет bridging (L2) внутри VLAN (VNI),
и routing (L3) между VLAN-ами.

2. Symmetric vs Asymmetric IRB

Asymmetric IRB (раньше часто применялся):
Клиент в VLAN10 шлёт на GW .254 → Leaf1 маршрутизирует сразу в VLAN20 → и шлёт VXLAN уже с VNI10200.
То есть инкапсуляция зависит от исходного сегмента.
Минус: для N сегментов нужно иметь N×N связей (все лифы должны уметь терминировать все L2VNI).
Symmetric IRB (современный подход, Arista/Cisco рекомендуют):
Клиент в VLAN10 шлёт на GW .254 → Leaf1 маршрутизирует в L3VNI (один общий, у нас VNI50000).
Трафик всегда идёт по VXLAN с L3VNI.
На удалённом Leaf3 трафик из L3VNI «высаживается» в VLAN20 (VNI10200).
Минус Asymmetric здесь устранён: маршрутизация симметрична, всегда через один L3VNI, независимо от исходного VLAN.

3. Механика Symmetric IRB

На каждом Leaf поднимаются SVI с Anycast GW (.254).
Все VRF имеют по одному L3VNI (у нас 50000).
Все маршруты сегментов (192.168.10.0/24, 192.168.20.0/24) анонсятся в EVPN как Type-5 префиксы с RT 50000:50000.
При маршрутизации:
Leaf1 принимает пакет в VLAN10.
Делаем L3 lookup в VRF → выбираем путь до 192.168.20.0/24.
Оборачиваем во VXLAN с L3VNI.
Удалённый Leaf3 получает → «понимает» что это L3VNI → высаживает в VLAN20.
