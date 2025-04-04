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
