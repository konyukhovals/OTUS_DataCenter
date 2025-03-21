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








