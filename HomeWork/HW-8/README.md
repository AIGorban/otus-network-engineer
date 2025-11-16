                                                              Курс "Дизайн сетей ЦОД" - OTUS.ru


                                                 Домашнее задание. 
*Цель:* **Реализовать передачу суммарных префиксов через EVPN route-type 5**


1. Разместить двух "клиентов" в разных VRF в рамках одной фабрики.
2. Настроить маршрутизацию между клиентами через внешнее устройство (граничный роутер\фаерволл\etc).
3. Зафиксировать в документации - план работы, адресное пространство, схему сети, настройки сетевого оборудования.

**1. Схема сети.**

![](Scheme/Scheme.png)

**2. Адресное пространство.** 

|      Spine1           |     Spine2             |         Leaf1          |       Leaf2            |     Leaf3              |
|-----------------------|------------------------|------------------------|------------------------|------------------------|
| Eth1/1 10.10.1.1/30   | Eth1/1 10.10.2.1/30    | Eth1/1 10.10.1.2/30    | Eth1/1 10.10.1.6/30    | Eth1/1 10.10.1.9/30    |
| Eth1/2 10.10.1.5/30   | Eth1/2 10.10.2.5/30    | Eth1/2 10.10.2.2/30    | Eth1/2 10.10.2.6/30    | Eth1/2 10.10.2.9/30    |
| Eth1/3 10.10.1.9/30   | Eth1/3 10.10.2.9/30    | Eth1/3 trunk           | Eth1/3 trunk           | Eth1/3 access 11       |
|                       |                        |                        |                        | Eth1/4 access 13       |  
| Lo0 10.10.10.10/32    | Lo0 10.10.10.20/32     | Lo0 10.10.10.1/32      | Lo0 10.10.10.2/32      | Lo0 10.10.10.3/32      |
|                       |                        | Lo105 10.10.105.1/32   | Lo105 10.10.105.2/32   | Lo105 10.10.105.3/32   | 

                                                     Адресация VPC             

|         VLAN          | VPC1 - VRF INSIDE_AA   |  VPC2 - VRF VRF_Main   | VPC3 - VRF INSIDE_AA   | VPC4 - VRF VRF_Main    |
|-----------------------|------------------------|------------------------|------------------------|------------------------|
|   VLAN **11** (GW .1) |  e0 192.168.11.10/28   |                        |   e0 192.168.11.5/28   |                        |  
|   VLAN **12** (GW .1) |                        |  e0 192.168.12.10/28   |                        |  e0 192.168.12.10/28   | 



                                               Автономные системы BGP (ASN)

|      Spine1      |     Spine2       |       Leaf1      |     Leaf2        |     Leaf3        |     R1           |
|------------------|------------------|------------------|------------------|------------------|------------------|
| ASN 65010        | ASN 65010        | ASN 65001        | ASN 65002        | ASN 65003        | ASN 65000        |

**3. Конфигурация оборудования (конфигурация SPINE не менялась).**

R1 - [Здесь](Configs/R1.txt)

Leaf1 -  [Здесь](Configs/Leaf1.txt)

Leaf3 -  [Здесь](Configs//Leaf3.txt)


**4. Проверка кооректности настроек**

   ***R1***  
        
    R1# sho ip route
        IP Route Table for VRF "default"
        '*' denotes best ucast next-hop
        '**' denotes best mcast next-hop
        '[x/y]' denotes [preference/metric]
        '%<string>' in via output denotes VRF <string>

        0.0.0.0/0, ubest/mbest: 1/0
            *via Null0, [1/0], 1d02h, static
        10.0.177.0/29, ubest/mbest: 1/0, attached
            *via 10.0.177.6, Vlan177, [0/0], 01:28:50, direct
        10.0.177.6/32, ubest/mbest: 1/0, attached
            *via 10.0.177.6, Vlan177, [0/0], 01:28:50, local
        10.0.199.0/29, ubest/mbest: 1/0, attached
            *via 10.0.199.6, Vlan199, [0/0], 01:28:35, direct
        10.0.199.6/32, ubest/mbest: 1/0, attached
            *via 10.0.199.6, Vlan199, [0/0], 01:28:35, local
        10.7.7.7/32, ubest/mbest: 2/0, attached
            *via 10.7.7.7, Lo0, [0/0], 1d02h, local
            *via 10.7.7.7, Lo0, [0/0], 1d02h, direct
        192.168.11.0/28, ubest/mbest: 1/0
            *via 10.0.177.3, [20/0], 01:23:59, bgp-65000, external, tag 65003
        192.168.11.5/32, ubest/mbest: 1/0
            *via 10.0.177.3, [1/0], 01:25:44, static
        192.168.11.10/32, ubest/mbest: 1/0
            *via 10.0.177.3, [20/0], 01:23:59, bgp-65000, external, tag 65003
        192.168.12.0/28, ubest/mbest: 1/0
            *via 10.0.199.3, [20/0], 01:23:50, bgp-65000, external, tag 65003
        192.168.12.5/32, ubest/mbest: 1/0
            *via 10.0.199.3, [1/0], 01:25:54, static
        192.168.12.10/32, ubest/mbest: 1/0
            *via 10.0.199.3, [20/0], 01:23:50, bgp-65000, external, tag 65003

   ***LEAF3***  

    Leaf3# sho ip  route vrf INSIDE_AA
        IP Route Table for VRF "INSIDE_AA"
        '*' denotes best ucast next-hop
        '**' denotes best mcast next-hop
        '[x/y]' denotes [preference/metric]
        '%<string>' in via output denotes VRF <string>

        10.0.177.0/29, ubest/mbest: 1/0, attached
            *via 10.0.177.3, Vlan177, [0/0], 01:13:09, direct
        10.0.177.3/32, ubest/mbest: 1/0, attached
            *via 10.0.177.3, Vlan177, [0/0], 01:13:09, local
        10.0.199.0/29, ubest/mbest: 1/0
            *via 10.0.177.6, [20/0], 01:04:03, bgp-65003, external, tag 65000
        10.0.199.6/32, ubest/mbest: 1/0
            *via 10.0.177.6, [20/0], 00:46:34, bgp-65003, external, tag 65000
        10.7.7.7/32, ubest/mbest: 1/0
            *via 10.0.177.6, [20/0], 01:04:03, bgp-65003, external, tag 65000
        192.168.11.0/28, ubest/mbest: 1/0, attached
            *via 192.168.11.1, Vlan11, [0/0], 1d19h, direct
        192.168.11.1/32, ubest/mbest: 1/0, attached
            *via 192.168.11.1, Vlan11, [0/0], 1d19h, local
        192.168.11.5/32, ubest/mbest: 1/0, attached
            *via 192.168.11.5, Vlan11, [190/0], 01:26:12, hmm
        192.168.11.10/32, ubest/mbest: 1/0
            *via 10.10.200.1%default, [20/0], 1d19h, bgp-65003, external, tag 65010 (evp
        n) segid: 7777 tunnelid: 0xa0ac801 encap: VXLAN

        192.168.12.5/32, ubest/mbest: 1/0
            *via 10.0.177.6, [20/0], 01:04:03, bgp-65003, external, tag 65000

    Leaf3# sho ip  route vrf VRF_MAIN
        IP Route Table for VRF "VRF_MAIN"
        '*' denotes best ucast next-hop
        '**' denotes best mcast next-hop
        '[x/y]' denotes [preference/metric]
        '%<string>' in via output denotes VRF <string>

        10.0.177.0/29, ubest/mbest: 1/0
            *via 10.0.199.6, [20/0], 01:04:03, bgp-65003, external, tag 65000
        10.0.177.6/32, ubest/mbest: 1/0
            *via 10.0.199.6, [20/0], 00:46:48, bgp-65003, external, tag 65000
        10.0.199.0/29, ubest/mbest: 1/0, attached
            *via 10.0.199.3, Vlan199, [0/0], 01:13:07, direct
        10.0.199.3/32, ubest/mbest: 1/0, attached
            *via 10.0.199.3, Vlan199, [0/0], 01:13:07, local
        10.7.7.7/32, ubest/mbest: 1/0
            *via 10.0.199.6, [20/0], 01:04:03, bgp-65003, external, tag 65000
        192.168.11.5/32, ubest/mbest: 1/0
            *via 10.0.199.6, [20/0], 01:04:03, bgp-65003, external, tag 65000
        192.168.12.0/28, ubest/mbest: 1/0, attached
            *via 192.168.12.1, Vlan12, [0/0], 1d19h, direct
        192.168.12.1/32, ubest/mbest: 1/0, attached
            *via 192.168.12.1, Vlan12, [0/0], 1d19h, local
        192.168.12.5/32, ubest/mbest: 1/0, attached
            *via 192.168.12.5, Vlan12, [190/0], 01:26:07, hmm
        192.168.12.10/32, ubest/mbest: 1/0
            *via 10.10.200.1%default, [20/0], 1d19h, bgp-65003, external, tag 65010 (evp
        n) segid: 9999 tunnelid: 0xa0ac801 encap: VXLAN  


  ***LEAF1***  

    Leaf1# sho ip route vrf INSIDE_AA
        IP Route Table for VRF "INSIDE_AA"
        '*' denotes best ucast next-hop
        '**' denotes best mcast next-hop
        '[x/y]' denotes [preference/metric]
        '%<string>' in via output denotes VRF <string>

        10.0.177.0/29, ubest/mbest: 1/0
            *via 10.10.200.3%default, [20/0], 01:35:12, bgp-65001, external, tag 65010 (
        evpn) segid: 7777 tunnelid: 0xa0ac803 encap: VXLAN

        10.0.177.6/32, ubest/mbest: 1/0
            *via 10.10.200.3%default, [20/0], 01:07:37, bgp-65001, external, tag 65010 (
        evpn) segid: 7777 tunnelid: 0xa0ac803 encap: VXLAN

        10.0.199.0/29, ubest/mbest: 1/0
            *via 10.10.200.3%default, [20/0], 01:25:01, bgp-65001, external, tag 65010 (
        evpn) segid: 7777 tunnelid: 0xa0ac803 encap: VXLAN

        10.0.199.6/32, ubest/mbest: 1/0
            *via 10.10.200.3%default, [20/0], 01:07:31, bgp-65001, external, tag 65010 (
        evpn) segid: 7777 tunnelid: 0xa0ac803 encap: VXLAN

        10.7.7.7/32, ubest/mbest: 1/0
            *via 10.10.200.3%default, [20/0], 01:25:01, bgp-65001, external, tag 65010 (
        evpn) segid: 7777 tunnelid: 0xa0ac803 encap: VXLAN

        192.168.11.0/28, ubest/mbest: 1/0, attached
            *via 192.168.11.1, Vlan11, [0/0], 2d20h, direct
        192.168.11.1/32, ubest/mbest: 1/0, attached
            *via 192.168.11.1, Vlan11, [0/0], 2d20h, local
        192.168.11.5/32, ubest/mbest: 1/0
            *via 10.10.200.3%default, [20/0], 01:47:09, bgp-65001, external, tag 65010 (
        evpn) segid: 7777 tunnelid: 0xa0ac803 encap: VXLAN

        192.168.11.10/32, ubest/mbest: 1/0, attached
            *via 192.168.11.10, Vlan11, [190/0], 2d20h, hmm
        192.168.12.5/32, ubest/mbest: 1/0
            *via 10.10.200.3%default, [20/0], 01:25:01, bgp-65001, external, tag 65010 (
        evpn) segid: 7777 tunnelid: 0xa0ac803 encap: VXLAN


        Leaf1# sho ip route vrf VRF_MAIN
        IP Route Table for VRF "VRF_MAIN"
        '*' denotes best ucast next-hop
        '**' denotes best mcast next-hop
        '[x/y]' denotes [preference/metric]
        '%<string>' in via output denotes VRF <string>

        10.0.177.0/29, ubest/mbest: 1/0
            *via 10.10.200.3%default, [20/0], 01:24:58, bgp-65001, external, tag 65010 (
        evpn) segid: 9999 tunnelid: 0xa0ac803 encap: VXLAN

        10.0.177.6/32, ubest/mbest: 1/0
            *via 10.10.200.3%default, [20/0], 01:07:43, bgp-65001, external, tag 65010 (
        evpn) segid: 9999 tunnelid: 0xa0ac803 encap: VXLAN

        10.0.199.0/29, ubest/mbest: 1/0
            *via 10.10.200.3%default, [20/0], 01:34:02, bgp-65001, external, tag 65010 (
        evpn) segid: 9999 tunnelid: 0xa0ac803 encap: VXLAN

        10.0.199.6/32, ubest/mbest: 1/0
            *via 10.10.200.3%default, [20/0], 01:07:37, bgp-65001, external, tag 65010 (
        evpn) segid: 9999 tunnelid: 0xa0ac803 encap: VXLAN

        10.7.7.7/32, ubest/mbest: 1/0
            *via 10.10.200.3%default, [20/0], 01:24:58, bgp-65001, external, tag 65010 (
        evpn) segid: 9999 tunnelid: 0xa0ac803 encap: VXLAN

        192.168.11.5/32, ubest/mbest: 1/0
            *via 10.10.200.3%default, [20/0], 01:24:58, bgp-65001, external, tag 65010 (
        evpn) segid: 9999 tunnelid: 0xa0ac803 encap: VXLAN

        192.168.12.0/28, ubest/mbest: 1/0, attached
            *via 192.168.12.1, Vlan12, [0/0], 1w1d, direct
        192.168.12.1/32, ubest/mbest: 1/0, attached
            *via 192.168.12.1, Vlan12, [0/0], 1w1d, local
        192.168.12.5/32, ubest/mbest: 1/0
            *via 10.10.200.3%default, [20/0], 01:47:01, bgp-65001, external, tag 65010 (
        evpn) segid: 9999 tunnelid: 0xa0ac803 encap: VXLAN

        192.168.12.10/32, ubest/mbest: 1/0, attached
            *via 192.168.12.10, Vlan12, [190/0], 1w0d, hmm