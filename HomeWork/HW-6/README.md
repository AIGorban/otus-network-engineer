                                                              Курс "Дизайн сетей ЦОД" - OTUS.ru


                                                 Домашнее задание. 
*Цель:* **Настроить маршрутизацию в рамках Overlay между клиентами.**


1. Настроить каждого клиента в своем VNI.
2. Настроить маршрутизацию между клиентами.
3. Зафиксировать в документации - план работы, адресное пространство, схему сети, конфигурацию устройств


**1. Схема сети.**

![](Scheme/Scheme.png)

**2. Адресное пространство.** 

|      Spine1           |     Spine2             |         Leaf1          |       Leaf2            |     Leaf3              |
|-----------------------|------------------------|------------------------|------------------------|------------------------|
| Eth1/1 10.10.1.1/30   | Eth1/1 10.10.2.1/30    | Eth1/1 10.10.1.2/30    | Eth1/1 10.10.1.6/30    | Eth1/1 10.10.1.9/30    |
| Eth1/2 10.10.1.5/30   | Eth1/2 10.10.2.5/30    | Eth1/2 10.10.2.2/30    | Eth1/2 10.10.2.6/30    | Eth1/2 10.10.2.9/30    |
| Eth1/3 10.10.1.9/30   | Eth1/3 10.10.2.9/30    | Eth1/3 access 11       | Eth1/3 access 12       | Eth1/3 access 11       |
|                       |                        |                        |                        | Eth1/4 access 13       |  
| Lo0 10.10.10.10/32    | Lo0 10.10.10.20/32     | Lo0 10.10.10.1/32      | Lo0 10.10.10.2/32      | Lo0 10.10.10.3/32      |


                                                     Адресация VPC             

|         VLAN          |      VPC1              |         VPC2           |          VPC3          |         VPC4           |
|-----------------------|------------------------|------------------------|------------------------|------------------------|
|   VLAN **11** (GW .1) | e0/0 192.168.111.10/28 |                        | e0/0 192.168.111.5/28 |                        |  
|   VLAN **12** (GW .1) |                        | e0/0 192.168.112.10/28 |                        |                        | 
|   VLAN **13** (GW .1) |                        |                        |                        | e0/0 192.168.113.10/28 | 


                                               Автономные системы BGP (ASN)

|      Spine1           |     Spine2            |         Leaf1         |       Leaf2           |     Leaf3             |
|-----------------------|-----------------------|-----------------------|-----------------------|-----------------------|
| ASN 65010             | ASN 65010             | ASN 65001             | ASN 65002             | ASN 65003             |


**3. Конфигурация оборудования.(Конфигурация SPINE не менялась)**

Spine1 - [Здесь](Configs/Spine1.txt)

Spine2 - [Здесь](Configs/Spine2.txt)

Leaf1 -  [Здесь](Configs/Leaf1.txt)

Leaf2 -  [Здесь](Configs/Leaf2.txt)

Leaf3 -  [Здесь](Configs/Leaf3.txt)

**4. Проверка связанности между сегментами сети.** 

*a. Проверяем доступность пакетами ICMP c VPC1 [192.168.11.10] до VPC2 [192.168.12.10], VPC3 [192.168.11.5], VPC4 [192.168.13.10]*

     VPC1> ping 192.168.11.5  
          84 bytes from 192.168.11.5 icmp_seq=1 ttl=64 time=12.067 ms
          84 bytes from 192.168.11.5 icmp_seq=2 ttl=64 time=12.270 ms
          84 bytes from 192.168.11.5 icmp_seq=3 ttl=64 time=11.937 ms
          84 bytes from 192.168.11.5 icmp_seq=4 ttl=64 time=12.109 ms
          84 bytes from 192.168.11.5 icmp_seq=5 ttl=64 time=12.467 ms

     VPC1> ping 192.168.12.10  
          84 bytes from 192.168.12.10 icmp_seq=1 ttl=62 time=15.216 ms
          84 bytes from 192.168.12.10 icmp_seq=2 ttl=62 time=13.305 ms
          84 bytes from 192.168.12.10 icmp_seq=3 ttl=62 time=13.148 ms
          84 bytes from 192.168.12.10 icmp_seq=4 ttl=62 time=11.580 ms
          84 bytes from 192.168.12.10 icmp_seq=5 ttl=62 time=12.650 ms

     VPC1> ping 192.168.13.10  
          84 bytes from 192.168.13.10 icmp_seq=1 ttl=62 time=12.708 ms
          84 bytes from 192.168.13.10 icmp_seq=2 ttl=62 time=12.682 ms
          84 bytes from 192.168.13.10 icmp_seq=3 ttl=62 time=13.518 ms
          84 bytes from 192.168.13.10 icmp_seq=4 ttl=62 time=14.560 ms
          84 bytes from 192.168.13.10 icmp_seq=5 ttl=62 time=13.844 ms

*b. Проверяем доступность пакетами ICMP c VPC3 [192.168.11.5] до VPC1 [192.168.11.10], VPC2 [192.168.12.10], VPC4 [192.168.13.10]*

     VPC3> ping 192.168.11.10
          84 bytes from 192.168.11.10 icmp_seq=1 ttl=64 time=13.778 ms
          84 bytes from 192.168.11.10 icmp_seq=2 ttl=64 time=11.365 ms
          84 bytes from 192.168.11.10 icmp_seq=3 ttl=64 time=10.970 ms
          84 bytes from 192.168.11.10 icmp_seq=4 ttl=64 time=11.310 ms
          84 bytes from 192.168.11.10 icmp_seq=5 ttl=64 time=11.733 ms

     VPC3> ping 192.168.12.10
          84 bytes from 192.168.12.10 icmp_seq=1 ttl=62 time=12.590 ms
          84 bytes from 192.168.12.10 icmp_seq=2 ttl=62 time=15.054 ms
          84 bytes from 192.168.12.10 icmp_seq=3 ttl=62 time=15.332 ms
          84 bytes from 192.168.12.10 icmp_seq=4 ttl=62 time=12.583 ms
          84 bytes from 192.168.12.10 icmp_seq=5 ttl=62 time=12.415 ms

     VPC3> ping 192.168.13.10
          84 bytes from 192.168.13.10 icmp_seq=1 ttl=63 time=5.686 ms
          84 bytes from 192.168.13.10 icmp_seq=2 ttl=63 time=5.813 ms
          84 bytes from 192.168.13.10 icmp_seq=3 ttl=63 time=5.907 ms
          84 bytes from 192.168.13.10 icmp_seq=4 ttl=63 time=5.752 ms
          84 bytes from 192.168.13.10 icmp_seq=5 ttl=63 time=6.199 ms


*c. Проверяем доступность пакетами ICMP c VPC2 [192.168.12.10] до VPC3 [192.168.11.5], VPC1 [192.168.11.10], VPC4 [192.168.13.10]*

     VPC2> ping 192.168.11.5
          84 bytes from 192.168.11.5 icmp_seq=1 ttl=62 time=13.311 ms
          84 bytes from 192.168.11.5 icmp_seq=2 ttl=62 time=13.769 ms
          84 bytes from 192.168.11.5 icmp_seq=3 ttl=62 time=12.653 ms
          84 bytes from 192.168.11.5 icmp_seq=4 ttl=62 time=14.847 ms
          84 bytes from 192.168.11.5 icmp_seq=5 ttl=62 time=12.236 ms

     VPC2> ping 192.168.11.10
          84 bytes from 192.168.11.10 icmp_seq=1 ttl=62 time=12.576 ms
          84 bytes from 192.168.11.10 icmp_seq=2 ttl=62 time=11.897 ms
          84 bytes from 192.168.11.10 icmp_seq=3 ttl=62 time=12.499 ms
          84 bytes from 192.168.11.10 icmp_seq=4 ttl=62 time=12.944 ms
          84 bytes from 192.168.11.10 icmp_seq=5 ttl=62 time=18.300 ms

     VPC2> ping 192.168.13.10
          84 bytes from 192.168.13.10 icmp_seq=1 ttl=62 time=15.301 ms
          84 bytes from 192.168.13.10 icmp_seq=2 ttl=62 time=13.260 ms
          84 bytes from 192.168.13.10 icmp_seq=3 ttl=62 time=13.291 ms
          84 bytes from 192.168.13.10 icmp_seq=4 ttl=62 time=12.593 ms
          84 bytes from 192.168.13.10 icmp_seq=5 ttl=62 time=12.121 ms
 