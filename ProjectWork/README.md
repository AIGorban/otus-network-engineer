                                                              Курс "Дизайн сетей ЦОД" - OTUS.ru  

                                                              ПРОЕКТНАЯ РАБОТА  
                                             "Проектирование сетевой фабрики на основне VxLAN EVPN"  

   ***ЦЕЛЬ:*** В существующей сетевой инфраструктуре развернуть резервную VxLAN фабрику в модели MultiPod. 

   ***ПРИМЕНЕННЫЕ ТЕХНОЛОГИИ:*** VxLAN, L2VPN, L3VPN, vPC. 


   **1. СХЕМА СЕТИ**

![](Scheme/Scheme.png)  

   **2. ТАБЛИЦЫ АДРЕСАЦИИ**

   **POD1 (Primary)**  

|      Spine1-1         |     Spine1-2          |         Leaf1-1       |       Leaf1-2         |      Leaf1-3          |       Leaf1-4        | 
|-----------------------|-----------------------|-----------------------|-----------------------|-----------------------|----------------------| 
| Eth1/1 10.0.251.0/31  | Eth1/1 10.0.252.0/31  | Eth1/1 10.0.251.1/31  | Eth1/1 10.0.251.3/31  | Eth1/1 10.0.251.5/31  | Eth1/1 10.0.251.7/31 | 
| Eth1/2 10.0.251.2/31  | Eth1/2 10.0.252.2/31  | Eth1/2 10.0.252.1/31  | Eth1/2 10.0.252.3/31  | Eth1/2 10.0.252.5/31  | Eth1/2 10.0.252.7/31 | 
| Eth1/3 10.0.251.4/31  | Eth1/3 10.0.252.4/31  | Eth1/5 192.168.0.9    | Eth1/5192.168.0.10    | Eth1/5 192.168.0.13   | Eth1/5 192.168.0.14  | 
| Eth1/4 10.0.251.6/31  | Eth1/4 10.0.252.6/31  | Eth1/6    TRUNK       | Eth1/6    TRUNK       | Eth1/6    TRUNK       | Eth1/6    TRUNK      | 
| Eth1/6 172.16.0.0/31  | Eth1/6 172.16.0.2/31  |                       |                       |                       |                      | 
|                       |                       |                       |                       |                       |                      | 
| Lo0 10.255.0.1/32     | Lo0 10.255.0.2/32     | Lo0 10.255.11.1/32    | Lo0 10.255.11.2/32    | Lo0 10.255.11.3/32    | Lo0 10.255.11.4/32   | 
|                       |                       | Lo5 10.255.5.101/32   | Lo5 10.255.5.102/32   | Lo5 10.255.5.103/32   | Lo5 10.255.5.104/32  | 

                                                     Адресация VPC - POD1             

|         VLAN          |      VPC14             |         VPC15          |          VPC16         |         VPC19          |  
|-----------------------|------------------------|------------------------|------------------------|------------------------|  
|   VLAN  **2** (GW .1) |                        |                        |                        |      192.168.2.20/24   |    
|   VLAN **12** (GW .1) |   192.168.12.10/24     |                        |                        |                        |   
|   VLAN **15** (GW .1) |                        |   192.168.15.10/24     |    192.168.15.20/24    |                        |   


   **POD2 (Secondary)**    

|      Spine2-1         |     Spine2-2           |         Leaf2-1        |       Leaf2-2          |
|-----------------------|------------------------|------------------------|------------------------|
| Eth1/1 10.5.251.0/31  | Eth1/1 10.5.252.0/31   | Eth1/1 10.5.251.1/31   | Eth1/1 10.5.251.3/31   |
| Eth1/2 10.5.251.2/31  | Eth1/2 10.5.252.2/31   | Eth1/2 10.5.252.1/31   | Eth1/2 10.5.252.3/31   |
| Eth1/6 172.16.0.1/31  | Eth1/6 172.16.0.3/31   | Eth1/5 192.168.0.17    | Eth1/5 192.168.0.18    |
|                       |                        | Eth1/6    TRUNK        | Eth1/6    TRUNK        |
|                       |                        |                        |                        |
|                       |                        |                        |                        |
| Lo0 10.255.5.1/32     | Lo0 10.255.5.2/32      | Lo0 10.255.21.1/32     | Lo0 10.255.21.2/32     |
|                       |                        | Lo5 10.255.5.105/32    | Lo5 10.255.5.106/32    |

                                                    Адресация VPC - POD2

|         VLAN          |      VPC17             |         VPC18          |  
|-----------------------|------------------------|------------------------|  
|   VLAN **2** (GW .1)  |                        |    192.168.2.10/24     |   
|   VLAN **12** (GW .1) |   192.168.12.20/24     |                        |  
|   VLAN **15** (GW .1) |                        |                        | 


   **Автономные системы BGP (ASN)**  

|      POD1 (Primary)      |    POD2 (Secondary)     |
|-----------------------|-----------------------|
|       ASN 65020       |      ASN 65010        |


   **2. КОНФИГУРАЦИЯ ОБОРУДОВАНИЯ**

**POD1 (Primary)**  

Spine1-1 - [Здесь](Configs/Spine1-1.txt)  
Spine1-2 - [Здесь](Configs/Spine1-2.txt)  
Leaf1-1 -  [Здесь](Configs/Leaf1-1.txt)  
Leaf1-2 -  [Здесь](Configs/Leaf1-2.txt)  
Leaf1-3 -  [Здесь](Configs/Leaf1-3.txt)  
Leaf1-4 -  [Здесь](Configs/Leaf1-4.txt)  

**POD2 (Secondary)**  

Spine2-1 - [Здесь](Configs/Spine2-1.txt)  
Spine2-2 - [Здесь](Configs/Spine2-2.txt)  
Leaf2-1 -  [Здесь](Configs/Leaf2-1.txt)  
Leaf2-2 -  [Здесь](Configs/Leaf2-2.txt)  

**3. ПРОВЕРКА НАСТРОЕК UNDRELAY**  

   **POD1 (Primary)**  

          Spine1-1# sho ip ospf neighbors  
          OSPF Process ID UNDERLAY VRF default  
          Total number of neighbors: 5  
          Neighbor ID     Pri State            Up Time  Address         Interface
          10.255.11.1       1 FULL/ -          11:19:39 10.0.251.1      Eth1/1
          10.255.11.2       1 FULL/ -          11:19:40 10.0.251.3      Eth1/2
          10.255.11.3       1 FULL/ -          07:29:28 10.0.251.5      Eth1/3
          10.255.11.4       1 FULL/ -          11:19:39 10.0.251.7      Eth1/4
          10.255.5.1        1 FULL/ -          11:19:41 172.16.0.1      Eth1/6

          Leaf1-3# sho ip ospf neighbors  
          OSPF Process ID UNDERLAY VRF default  
          Total number of neighbors: 2  
          Neighbor ID     Pri State            Up Time  Address         Interface
          10.255.0.1        1 FULL/ -          07:30:01 10.0.251.4      Eth1/1
          10.255.0.2        1 FULL/ -          07:29:58 10.0.252.4      Eth1/2  

.

    Spine1-1# sho ip route
    IP Route Table for VRF "default"
    '*' denotes best ucast next-hop
    '**' denotes best mcast next-hop
    '[x/y]' denotes [preference/metric]
    '%<string>' in via output denotes VRF <string>

    10.0.251.0/31, ubest/mbest: 1/0, attached
        *via 10.0.251.0, Eth1/1, [0/0], 11:17:40, direct
    10.0.251.0/32, ubest/mbest: 1/0, attached
        *via 10.0.251.0, Eth1/1, [0/0], 11:17:40, local
    10.0.251.2/31, ubest/mbest: 1/0, attached
        *via 10.0.251.2, Eth1/2, [0/0], 11:17:40, direct
    10.0.251.2/32, ubest/mbest: 1/0, attached
        *via 10.0.251.2, Eth1/2, [0/0], 11:17:40, local
    10.0.251.4/31, ubest/mbest: 1/0, attached
        *via 10.0.251.4, Eth1/3, [0/0], 11:17:40, direct
    10.0.251.4/32, ubest/mbest: 1/0, attached
        *via 10.0.251.4, Eth1/3, [0/0], 11:17:40, local
    10.0.251.6/31, ubest/mbest: 1/0, attached
        *via 10.0.251.6, Eth1/4, [0/0], 11:17:40, direct
    10.0.251.6/32, ubest/mbest: 1/0, attached
        *via 10.0.251.6, Eth1/4, [0/0], 11:17:40, local
    10.0.252.0/31, ubest/mbest: 1/0
        *via 10.0.251.1, Eth1/1, [110/80], 11:17:24, ospf-UNDERLAY, intra
    10.0.252.2/31, ubest/mbest: 1/0
        *via 10.0.251.3, Eth1/2, [110/80], 11:17:24, ospf-UNDERLAY, intra
    10.0.252.4/31, ubest/mbest: 1/0
        *via 10.0.251.5, Eth1/3, [110/80], 07:27:14, ospf-UNDERLAY, intra
    10.0.252.6/31, ubest/mbest: 1/0
        *via 10.0.251.7, Eth1/4, [110/80], 11:17:24, ospf-UNDERLAY, intra
    10.5.251.0/31, ubest/mbest: 4/0
        *via 10.0.251.1, Eth1/1, [110/20], 07:27:14, ospf-UNDERLAY, type-2
        *via 10.0.251.3, Eth1/2, [110/20], 07:27:14, ospf-UNDERLAY, type-2
        *via 10.0.251.5, Eth1/3, [110/20], 07:27:14, ospf-UNDERLAY, type-2
        *via 10.0.251.7, Eth1/4, [110/20], 07:27:14, ospf-UNDERLAY, type-2
    10.5.251.2/31, ubest/mbest: 4/0
        *via 10.0.251.1, Eth1/1, [110/20], 07:27:14, ospf-UNDERLAY, type-2
        *via 10.0.251.3, Eth1/2, [110/20], 07:27:14, ospf-UNDERLAY, type-2
        *via 10.0.251.5, Eth1/3, [110/20], 07:27:14, ospf-UNDERLAY, type-2
        *via 10.0.251.7, Eth1/4, [110/20], 07:27:14, ospf-UNDERLAY, type-2
    10.5.252.0/31, ubest/mbest: 1/0
        *via 172.16.0.1, Eth1/6, [110/20], 11:17:24, ospf-UNDERLAY, type-2
    10.5.252.2/31, ubest/mbest: 1/0
        *via 172.16.0.1, Eth1/6, [110/20], 11:17:24, ospf-UNDERLAY, type-2
    10.255.0.1/32, ubest/mbest: 2/0, attached
        *via 10.255.0.1, Lo0, [0/0], 11:18:25, local
        *via 10.255.0.1, Lo0, [0/0], 11:18:25, direct
    10.255.0.2/32, ubest/mbest: 4/0
        *via 10.0.251.1, Eth1/1, [110/81], 07:27:14, ospf-UNDERLAY, intra
        *via 10.0.251.3, Eth1/2, [110/81], 07:27:14, ospf-UNDERLAY, intra
        *via 10.0.251.5, Eth1/3, [110/81], 07:27:14, ospf-UNDERLAY, intra
        *via 10.0.251.7, Eth1/4, [110/81], 07:27:14, ospf-UNDERLAY, intra
    10.255.5.1/32, ubest/mbest: 1/0
        *via 172.16.0.1, [20/0], 11:17:21, bgp-65020, external, tag 65010
    10.255.5.2/32, ubest/mbest: 4/0
        *via 10.0.251.1, Eth1/1, [110/121], 07:27:14, ospf-UNDERLAY, intra
        *via 10.0.251.3, Eth1/2, [110/121], 07:27:14, ospf-UNDERLAY, intra
        *via 10.0.251.5, Eth1/3, [110/121], 07:27:14, ospf-UNDERLAY, intra
        *via 10.0.251.7, Eth1/4, [110/121], 07:27:14, ospf-UNDERLAY, intra
    10.255.5.101/32, ubest/mbest: 1/0
        *via 10.0.251.1, Eth1/1, [110/41], 11:17:24, ospf-UNDERLAY, intra
    10.255.5.102/32, ubest/mbest: 1/0
        *via 10.0.251.3, Eth1/2, [110/41], 11:17:24, ospf-UNDERLAY, intra
    10.255.5.103/32, ubest/mbest: 1/0
        *via 10.0.251.5, Eth1/3, [110/41], 05:34:54, ospf-UNDERLAY, intra
    10.255.5.104/32, ubest/mbest: 1/0
        *via 10.0.251.7, Eth1/4, [110/41], 05:34:54, ospf-UNDERLAY, intra
    10.255.5.105/32, ubest/mbest: 1/0
        *via 172.16.0.1, Eth1/6, [110/20], 03:46:20, ospf-UNDERLAY, type-2
    10.255.5.106/32, ubest/mbest: 4/0
        *via 10.0.251.1, Eth1/1, [110/20], 03:55:26, ospf-UNDERLAY, type-2
        *via 10.0.251.3, Eth1/2, [110/20], 03:55:26, ospf-UNDERLAY, type-2
        *via 10.0.251.5, Eth1/3, [110/20], 03:55:26, ospf-UNDERLAY, type-2
        *via 10.0.251.7, Eth1/4, [110/20], 03:55:26, ospf-UNDERLAY, type-2
    10.255.11.1/32, ubest/mbest: 1/0
        *via 10.0.251.1, Eth1/1, [110/41], 11:17:24, ospf-UNDERLAY, intra
    10.255.11.2/32, ubest/mbest: 1/0
        *via 10.0.251.3, Eth1/2, [110/41], 11:17:24, ospf-UNDERLAY, intra
    10.255.11.3/32, ubest/mbest: 1/0
        *via 10.0.251.5, Eth1/3, [110/41], 06:20:55, ospf-UNDERLAY, intra
    10.255.11.4/32, ubest/mbest: 1/0
        *via 10.0.251.7, Eth1/4, [110/41], 05:40:53, ospf-UNDERLAY, intra
    10.255.21.1/32, ubest/mbest: 1/0
        *via 172.16.0.1, [20/0], 04:08:25, bgp-65020, external, tag 65010
    10.255.21.2/32, ubest/mbest: 1/0
        *via 172.16.0.1, [20/0], 00:00:39, bgp-65020, external, tag 65010
    10.255.50.101/32, ubest/mbest: 2/0
        *via 10.0.251.1, Eth1/1, [110/41], 11:17:24, ospf-UNDERLAY, intra
        *via 10.0.251.3, Eth1/2, [110/41], 11:17:24, ospf-UNDERLAY, intra
    10.255.50.102/32, ubest/mbest: 2/0
        *via 10.0.251.5, Eth1/3, [110/41], 05:34:54, ospf-UNDERLAY, intra
        *via 10.0.251.7, Eth1/4, [110/41], 05:34:54, ospf-UNDERLAY, intra
    10.255.50.105/32, ubest/mbest: 4/0
        *via 10.0.251.1, Eth1/1, [110/20], 03:55:26, ospf-UNDERLAY, type-2
        *via 10.0.251.3, Eth1/2, [110/20], 03:55:26, ospf-UNDERLAY, type-2
        *via 10.0.251.5, Eth1/3, [110/20], 03:55:26, ospf-UNDERLAY, type-2
        *via 10.0.251.7, Eth1/4, [110/20], 03:55:26, ospf-UNDERLAY, type-2
    172.16.0.0/31, ubest/mbest: 1/0, attached
        *via 172.16.0.0, Eth1/6, [0/0], 11:17:40, direct
    172.16.0.0/32, ubest/mbest: 1/0, attached
        *via 172.16.0.0, Eth1/6, [0/0], 11:17:40, local
    172.16.0.2/31, ubest/mbest: 4/0
        *via 10.0.251.1, Eth1/1, [110/120], 07:27:14, ospf-UNDERLAY, intra
        *via 10.0.251.3, Eth1/2, [110/120], 07:27:14, ospf-UNDERLAY, intra
        *via 10.0.251.5, Eth1/3, [110/120], 07:27:14, ospf-UNDERLAY, intra
        *via 10.0.251.7, Eth1/4, [110/120], 07:27:14, ospf-UNDERLAY, intra


    Leaf1-3# sho ip route
    IP Route Table for VRF "default"
    '*' denotes best ucast next-hop
    '**' denotes best mcast next-hop
    '[x/y]' denotes [preference/metric]
    '%<string>' in via output denotes VRF <string>

    10.0.251.0/31, ubest/mbest: 1/0
        *via 10.0.251.4, Eth1/1, [110/80], 07:28:07, ospf-UNDERLAY, intra
    10.0.251.2/31, ubest/mbest: 1/0
        *via 10.0.251.4, Eth1/1, [110/80], 07:28:07, ospf-UNDERLAY, intra
    10.0.251.4/31, ubest/mbest: 1/0, attached
        *via 10.0.251.5, Eth1/1, [0/0], 07:28:20, direct
    10.0.251.5/32, ubest/mbest: 1/0, attached
        *via 10.0.251.5, Eth1/1, [0/0], 07:28:20, local
    10.0.251.6/31, ubest/mbest: 1/0
        *via 10.0.251.4, Eth1/1, [110/80], 07:28:07, ospf-UNDERLAY, intra
    10.0.252.0/31, ubest/mbest: 1/0
        *via 10.0.252.4, Eth1/2, [110/80], 07:28:07, ospf-UNDERLAY, intra
    10.0.252.2/31, ubest/mbest: 1/0
        *via 10.0.252.4, Eth1/2, [110/80], 07:28:07, ospf-UNDERLAY, intra
    10.0.252.4/31, ubest/mbest: 1/0, attached
        *via 10.0.252.5, Eth1/2, [0/0], 07:28:20, direct
    10.0.252.5/32, ubest/mbest: 1/0, attached
        *via 10.0.252.5, Eth1/2, [0/0], 07:28:20, local
    10.0.252.6/31, ubest/mbest: 1/0
        *via 10.0.252.4, Eth1/2, [110/80], 07:28:07, ospf-UNDERLAY, intra
    10.5.251.0/31, ubest/mbest: 1/0
        *via 10.0.252.4, Eth1/2, [110/20], 07:28:07, ospf-UNDERLAY, type-2
    10.5.251.2/31, ubest/mbest: 1/0
        *via 10.0.252.4, Eth1/2, [110/20], 07:28:07, ospf-UNDERLAY, type-2
    10.5.252.0/31, ubest/mbest: 1/0
        *via 10.0.251.4, Eth1/1, [110/20], 07:28:07, ospf-UNDERLAY, type-2
    10.5.252.2/31, ubest/mbest: 1/0
        *via 10.0.251.4, Eth1/1, [110/20], 07:28:07, ospf-UNDERLAY, type-2
    10.255.0.1/32, ubest/mbest: 1/0
        *via 10.0.251.4, Eth1/1, [110/41], 07:28:07, ospf-UNDERLAY, intra
    10.255.0.2/32, ubest/mbest: 1/0
        *via 10.0.252.4, Eth1/2, [110/41], 07:28:07, ospf-UNDERLAY, intra
    10.255.5.1/32, ubest/mbest: 1/0
        *via 10.0.251.4, Eth1/1, [110/81], 07:28:07, ospf-UNDERLAY, intra
    10.255.5.2/32, ubest/mbest: 1/0
        *via 10.0.252.4, Eth1/2, [110/81], 07:28:07, ospf-UNDERLAY, intra
    10.255.5.101/32, ubest/mbest: 2/0
        *via 10.0.251.4, Eth1/1, [110/81], 07:28:07, ospf-UNDERLAY, intra
        *via 10.0.252.4, Eth1/2, [110/81], 07:28:07, ospf-UNDERLAY, intra
    10.255.5.102/32, ubest/mbest: 2/0
        *via 10.0.251.4, Eth1/1, [110/81], 07:28:07, ospf-UNDERLAY, intra
        *via 10.0.252.4, Eth1/2, [110/81], 07:28:07, ospf-UNDERLAY, intra
    10.255.5.103/32, ubest/mbest: 2/0, attached
        *via 10.255.5.103, Lo5, [0/0], 05:35:47, local
        *via 10.255.5.103, Lo5, [0/0], 05:35:47, direct
    10.255.5.104/32, ubest/mbest: 2/0
        *via 10.0.251.4, Eth1/1, [110/81], 05:35:47, ospf-UNDERLAY, intra
        *via 10.0.252.4, Eth1/2, [110/81], 05:35:47, ospf-UNDERLAY, intra
    10.255.5.105/32, ubest/mbest: 1/0
        *via 10.0.251.4, Eth1/1, [110/20], 03:47:07, ospf-UNDERLAY, type-2
    10.255.5.106/32, ubest/mbest: 1/0
        *via 10.0.252.4, Eth1/2, [110/20], 03:56:18, ospf-UNDERLAY, type-2
    10.255.11.1/32, ubest/mbest: 2/0
        *via 10.0.251.4, Eth1/1, [110/81], 07:28:07, ospf-UNDERLAY, intra
        *via 10.0.252.4, Eth1/2, [110/81], 07:28:07, ospf-UNDERLAY, intra
    10.255.11.2/32, ubest/mbest: 2/0
        *via 10.0.251.4, Eth1/1, [110/81], 07:28:07, ospf-UNDERLAY, intra
        *via 10.0.252.4, Eth1/2, [110/81], 07:28:07, ospf-UNDERLAY, intra
    10.255.11.3/32, ubest/mbest: 2/0, attached
        *via 10.255.11.3, Lo0, [0/0], 06:21:48, local
        *via 10.255.11.3, Lo0, [0/0], 06:21:48, direct
    10.255.11.4/32, ubest/mbest: 2/0
        *via 10.0.251.4, Eth1/1, [110/81], 05:41:46, ospf-UNDERLAY, intra
        *via 10.0.252.4, Eth1/2, [110/81], 05:41:46, ospf-UNDERLAY, intra
    10.255.21.1/32, ubest/mbest: 1/0
        *via 10.0.251.4, Eth1/1, [110/20], 07:28:07, ospf-UNDERLAY, type-2
    10.255.21.2/32, ubest/mbest: 1/0
        *via 10.0.252.4, Eth1/2, [110/20], 07:28:07, ospf-UNDERLAY, type-2
    10.255.50.101/32, ubest/mbest: 2/0
        *via 10.0.251.4, Eth1/1, [110/81], 07:28:07, ospf-UNDERLAY, intra
        *via 10.0.252.4, Eth1/2, [110/81], 07:28:07, ospf-UNDERLAY, intra
    10.255.50.102/32, ubest/mbest: 2/0, attached
        *via 10.255.50.102, Lo5, [0/0], 05:35:47, local
        *via 10.255.50.102, Lo5, [0/0], 05:35:47, direct
    10.255.50.105/32, ubest/mbest: 1/0
        *via 10.0.252.4, Eth1/2, [110/20], 03:56:18, ospf-UNDERLAY, type-2
    172.16.0.0/31, ubest/mbest: 1/0
        *via 10.0.251.4, Eth1/1, [110/80], 07:28:07, ospf-UNDERLAY, intra
    172.16.0.2/31, ubest/mbest: 1/0
        *via 10.0.252.4, Eth1/2, [110/80], 07:28:07, ospf-UNDERLAY, intra

   **POD2 (Secondary)** 
    
    Spine2-2# sho isis database
    IS-IS Process: UNDERLAY LSP database VRF: default
    IS-IS Level-1 Link State Database
    LSPID                 Seq Number   Checksum  Lifetime   A/P/O/T
    Spine2-1.00-00        0x0000025C   0x3C10    978        0/0/0/3
    Spine2-2.00-00      * 0x00000247   0xE257    1009       0/0/0/3
    Leaf2-1.00-00         0x000001FF   0x63CB    942        0/0/0/1
    Leaf2-2.00-00         0x000001CD   0x1A3D    854        0/0/0/1

    IS-IS Level-2 Link State Database
    LSPID                 Seq Number   Checksum  Lifetime   A/P/O/T
    Spine2-2.00-00      * 0x00000148   0x6675    1025       0/0/0/3


    Leaf2-1# sho isis database
    IS-IS Process: UNDERLAY LSP database VRF: default
    IS-IS Level-1 Link State Database
    LSPID                 Seq Number   Checksum  Lifetime   A/P/O/T
    Spine2-1.00-00        0x0000025C   0x3C10    891        0/0/0/3
    Spine2-2.00-00        0x00000247   0xE257    920        0/0/0/3
    Leaf2-1.00-00       * 0x000001FF   0x63CB    855        0/0/0/1
    Leaf2-2.00-00         0x000001CD   0x1A3D    765        0/0/0/1

    IS-IS Level-2 Link State Database
      LSPID                 Seq Number   Checksum  Lifetime   A/P/O/T

.  

    Spine2-2# sho ip route
    IP Route Table for VRF "default"
    '*' denotes best ucast next-hop
    '**' denotes best mcast next-hop
    '[x/y]' denotes [preference/metric]
    '%<string>' in via output denotes VRF <string>
 

    10.0.251.0/31, ubest/mbest: 1/0
        *via 172.16.0.2, Eth1/6, [110/120], 1d02h, ospf-UNDERLAY, intra
    10.0.251.2/31, ubest/mbest: 1/0
        *via 172.16.0.2, Eth1/6, [110/120], 1d18h, ospf-UNDERLAY, intra
    10.0.251.4/31, ubest/mbest: 1/0
        *via 172.16.0.2, Eth1/6, [110/120], 08:14:00, ospf-UNDERLAY, intra
    10.0.251.6/31, ubest/mbest: 1/0
        *via 172.16.0.2, Eth1/6, [110/120], 20:40:24, ospf-UNDERLAY, intra
    10.0.252.0/31, ubest/mbest: 1/0
        *via 172.16.0.2, Eth1/6, [110/80], 1d18h, ospf-UNDERLAY, intra
    10.0.252.2/31, ubest/mbest: 1/0
        *via 172.16.0.2, Eth1/6, [110/80], 1d18h, ospf-UNDERLAY, intra
    10.0.252.4/31, ubest/mbest: 1/0
        *via 172.16.0.2, Eth1/6, [110/80], 1d18h, ospf-UNDERLAY, intra
    10.0.252.6/31, ubest/mbest: 1/0
        *via 172.16.0.2, Eth1/6, [110/80], 1d18h, ospf-UNDERLAY, intra
    10.5.251.0/31, ubest/mbest: 1/0
        *via 10.5.252.1, Eth1/1, [115/80], 1d18h, isis-UNDERLAY, L1
    10.5.251.2/31, ubest/mbest: 1/0
        *via 10.5.252.3, Eth1/2, [115/80], 08:14:52, isis-UNDERLAY, L1
    10.5.252.0/31, ubest/mbest: 1/0, attached
        *via 10.5.252.0, Eth1/1, [0/0], 1d18h, direct
    10.5.252.0/32, ubest/mbest: 1/0, attached
        *via 10.5.252.0, Eth1/1, [0/0], 1d18h, local
    10.5.252.2/31, ubest/mbest: 1/0, attached
        *via 10.5.252.2, Eth1/2, [0/0], 1d18h, direct
    10.5.252.2/32, ubest/mbest: 1/0, attached
        *via 10.5.252.2, Eth1/2, [0/0], 1d18h, local
    10.255.0.1/32, ubest/mbest: 1/0
        *via 172.16.0.2, Eth1/6, [110/121], 12:04:13, ospf-UNDERLAY, intra
    10.255.0.2/32, ubest/mbest: 1/0
        *via 172.16.0.2, [20/0], 1d18h, bgp-65010, external, tag 65020
    10.255.5.1/32, ubest/mbest: 1/0
        *via 172.16.0.2, Eth1/6, [110/161], 12:04:13, ospf-UNDERLAY, intra
    10.255.5.2/32, ubest/mbest: 2/0, attached
        *via 10.255.5.2, Lo0, [0/0], 1d18h, local
        *via 10.255.5.2, Lo0, [0/0], 1d18h, direct
    10.255.5.101/32, ubest/mbest: 1/0
        *via 172.16.0.2, Eth1/6, [110/81], 20:22:57, ospf-UNDERLAY, intra
    10.255.5.102/32, ubest/mbest: 1/0
        *via 172.16.0.2, Eth1/6, [110/81], 20:25:22, ospf-UNDERLAY, intra
    10.255.5.103/32, ubest/mbest: 1/0
        *via 172.16.0.2, Eth1/6, [110/81], 06:21:40, ospf-UNDERLAY, intra
    10.255.5.104/32, ubest/mbest: 1/0
        *via 172.16.0.2, Eth1/6, [110/81], 06:21:40, ospf-UNDERLAY, intra
    10.255.5.105/32, ubest/mbest: 1/0
        *via 172.16.0.2, Eth1/6, [110/20], 04:33:05, ospf-UNDERLAY, type-2
    10.255.5.106/32, ubest/mbest: 1/0
        *via 10.5.252.3, Eth1/2, [115/41], 04:33:06, isis-UNDERLAY, L1
    10.255.11.1/32, ubest/mbest: 1/0
        *via 172.16.0.2, [20/0], 05:43:40, bgp-65010, external, tag 65020
    10.255.11.2/32, ubest/mbest: 1/0
        *via 172.16.0.2, [20/0], 05:43:39, bgp-65010, external, tag 65020
    10.255.11.3/32, ubest/mbest: 1/0
        *via 172.16.0.2, [20/0], 05:43:38, bgp-65010, external, tag 65020
    10.255.11.4/32, ubest/mbest: 1/0
        *via 172.16.0.2, [20/0], 05:43:37, bgp-65010, external, tag 65020
    10.255.21.1/32, ubest/mbest: 1/0
        *via 172.16.0.2, Eth1/6, [110/20], 12:04:13, ospf-UNDERLAY, type-2
    10.255.21.2/32, ubest/mbest: 1/0
        *via 10.5.252.3, Eth1/2, [115/41], 04:41:24, isis-UNDERLAY, L1
    10.255.50.101/32, ubest/mbest: 1/0
        *via 172.16.0.2, Eth1/6, [110/81], 20:28:34, ospf-UNDERLAY, intra
    10.255.50.102/32, ubest/mbest: 1/0
        *via 172.16.0.2, Eth1/6, [110/81], 06:21:40, ospf-UNDERLAY, intra
    10.255.50.105/32, ubest/mbest: 2/0
        *via 10.5.252.1, Eth1/1, [115/41], 04:33:06, isis-UNDERLAY, L1
        *via 10.5.252.3, Eth1/2, [115/41], 04:33:06, isis-UNDERLAY, L1
    172.16.0.0/31, ubest/mbest: 1/0
        *via 172.16.0.2, Eth1/6, [110/160], 12:04:13, ospf-UNDERLAY, intra
    172.16.0.2/31, ubest/mbest: 1/0, attached
        *via 172.16.0.3, Eth1/6, [0/0], 1d18h, direct
    172.16.0.3/32, ubest/mbest: 1/0, attached
        *via 172.16.0.3, Eth1/6, [0/0], 1d18h, local  

    Leaf2-1# sho ip route
    IP Route Table for VRF "default"
    '*' denotes best ucast next-hop
    '**' denotes best mcast next-hop
    '[x/y]' denotes [preference/metric]
    '%<string>' in via output denotes VRF <string>
 

    10.0.251.0/31, ubest/mbest: 2/0
        *via 10.5.251.0, Eth1/1, [115/50], 12:05:22, isis-UNDERLAY, L1
        *via 10.5.252.0, Eth1/2, [115/50], 1d18h, isis-UNDERLAY, L1
    10.0.251.2/31, ubest/mbest: 2/0
        *via 10.5.251.0, Eth1/1, [115/50], 12:05:22, isis-UNDERLAY, L1
        *via 10.5.252.0, Eth1/2, [115/50], 1d18h, isis-UNDERLAY, L1
    10.0.251.4/31, ubest/mbest: 2/0
        *via 10.5.251.0, Eth1/1, [115/50], 12:05:22, isis-UNDERLAY, L1
        *via 10.5.252.0, Eth1/2, [115/50], 1d18h, isis-UNDERLAY, L1
    10.0.251.6/31, ubest/mbest: 2/0
        *via 10.5.251.0, Eth1/1, [115/50], 12:05:22, isis-UNDERLAY, L1
        *via 10.5.252.0, Eth1/2, [115/50], 1d18h, isis-UNDERLAY, L1
    10.0.252.0/31, ubest/mbest: 2/0
        *via 10.5.251.0, Eth1/1, [115/50], 12:05:22, isis-UNDERLAY, L1
        *via 10.5.252.0, Eth1/2, [115/50], 1d18h, isis-UNDERLAY, L1
    10.0.252.2/31, ubest/mbest: 2/0
        *via 10.5.251.0, Eth1/1, [115/50], 12:05:22, isis-UNDERLAY, L1
        *via 10.5.252.0, Eth1/2, [115/50], 1d18h, isis-UNDERLAY, L1
    10.0.252.4/31, ubest/mbest: 2/0
        *via 10.5.251.0, Eth1/1, [115/50], 12:05:22, isis-UNDERLAY, L1
        *via 10.5.252.0, Eth1/2, [115/50], 1d18h, isis-UNDERLAY, L1
    10.0.252.6/31, ubest/mbest: 2/0
        *via 10.5.251.0, Eth1/1, [115/50], 12:05:22, isis-UNDERLAY, L1
        *via 10.5.252.0, Eth1/2, [115/50], 1d18h, isis-UNDERLAY, L1
    10.5.251.0/31, ubest/mbest: 1/0, attached
        *via 10.5.251.1, Eth1/1, [0/0], 3d03h, direct
    10.5.251.1/32, ubest/mbest: 1/0, attached
        *via 10.5.251.1, Eth1/1, [0/0], 3d03h, local
    10.5.251.2/31, ubest/mbest: 1/0
        *via 10.5.251.0, Eth1/1, [115/80], 3d03h, isis-UNDERLAY, L1
    10.5.252.0/31, ubest/mbest: 1/0, attached
        *via 10.5.252.1, Eth1/2, [0/0], 3d03h, direct
    10.5.252.1/32, ubest/mbest: 1/0, attached
        *via 10.5.252.1, Eth1/2, [0/0], 3d03h, local
    10.5.252.2/31, ubest/mbest: 1/0
        *via 10.5.252.0, Eth1/2, [115/80], 1d18h, isis-UNDERLAY, L1
    10.255.0.1/32, ubest/mbest: 1/0
        *via 10.5.252.0, Eth1/2, [115/50], 12:05:25, isis-UNDERLAY, L1
    10.255.0.2/32, ubest/mbest: 1/0
        *via 10.5.251.0, Eth1/1, [115/50], 12:05:22, isis-UNDERLAY, L1
    10.255.5.1/32, ubest/mbest: 1/0
        *via 10.5.252.0, Eth1/2, [115/50], 12:05:25, isis-UNDERLAY, L1
    10.255.5.2/32, ubest/mbest: 1/0
        *via 10.5.251.0, Eth1/1, [115/50], 12:05:22, isis-UNDERLAY, L1
    10.255.5.101/32, ubest/mbest: 2/0
        *via 10.5.251.0, Eth1/1, [115/50], 12:05:22, isis-UNDERLAY, L1
        *via 10.5.252.0, Eth1/2, [115/50], 20:24:21, isis-UNDERLAY, L1
    10.255.5.102/32, ubest/mbest: 2/0
        *via 10.5.251.0, Eth1/1, [115/50], 12:05:22, isis-UNDERLAY, L1
        *via 10.5.252.0, Eth1/2, [115/50], 20:26:35, isis-UNDERLAY, L1
    10.255.5.103/32, ubest/mbest: 2/0
        *via 10.5.251.0, Eth1/1, [115/50], 07:08:52, isis-UNDERLAY, L1
        *via 10.5.252.0, Eth1/2, [115/50], 06:22:52, isis-UNDERLAY, L1
    10.255.5.104/32, ubest/mbest: 2/0
        *via 10.5.251.0, Eth1/1, [115/50], 06:22:52, isis-UNDERLAY, L1
        *via 10.5.252.0, Eth1/2, [115/50], 06:22:52, isis-UNDERLAY, L1
    10.255.5.105/32, ubest/mbest: 2/0, attached
        *via 10.255.5.105, Lo5, [0/0], 04:34:19, local
    *via 10.255.5.105, Lo5, [0/0], 04:34:19, direct
    10.255.5.106/32, ubest/mbest: 1/0
        *via 10.5.251.0, Eth1/1, [115/50], 04:43:29, isis-UNDERLAY, L1
    10.255.11.1/32, ubest/mbest: 1/0
        *via 10.255.5.1, [200/0], 04:56:23, bgp-65010, internal, tag 65020
    10.255.11.2/32, ubest/mbest: 1/0
        *via 10.255.5.1, [200/0], 04:56:23, bgp-65010, internal, tag 65020
    10.255.11.3/32, ubest/mbest: 1/0
        *via 10.255.5.1, [200/0], 04:56:23, bgp-65010, internal, tag 65020
    10.255.11.4/32, ubest/mbest: 1/0
        *via 10.255.5.1, [200/0], 04:56:23, bgp-65010, internal, tag 65020
    10.255.21.1/32, ubest/mbest: 2/0, attached
        *via 10.255.21.1, Lo0, [0/0], 04:56:28, local
        *via 10.255.21.1, Lo0, [0/0], 04:56:28, direct
    10.255.21.2/32, ubest/mbest: 1/0
        *via 10.5.251.0, Eth1/1, [115/50], 12:05:02, isis-UNDERLAY, L1
    10.255.50.101/32, ubest/mbest: 2/0
        *via 10.5.251.0, Eth1/1, [115/50], 12:05:22, isis-UNDERLAY, L1
        *via 10.5.252.0, Eth1/2, [115/50], 20:29:46, isis-UNDERLAY, L1
    10.255.50.102/32, ubest/mbest: 2/0
        *via 10.5.251.0, Eth1/1, [115/50], 07:08:52, isis-UNDERLAY, L1
        *via 10.5.252.0, Eth1/2, [115/50], 06:22:52, isis-UNDERLAY, L1
    10.255.50.105/32, ubest/mbest: 2/0, attached
        *via 10.255.50.105, Lo5, [0/0], 04:34:19, local
        *via 10.255.50.105, Lo5, [0/0], 04:34:19, direct
    172.16.0.0/31, ubest/mbest: 1/0
        *via 10.5.252.0, Eth1/2, [115/50], 12:05:25, isis-UNDERLAY, L1
    172.16.0.2/31, ubest/mbest: 1/0
        *via 10.5.251.0, Eth1/1, [115/50], 12:05:22, isis-UNDERLAY, L1

**3. ПРОВЕРКА НАСТРОЕК OVERLAY**    

   **POD1 (Primary)**    