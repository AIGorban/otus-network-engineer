                                                              Курс "Дизайн сетей ЦОД" - OTUS.ru  

                                                              ПРОЕКТНАЯ РАБОТА  
                                             "Проектирование сетевой фабрики на основне VxLAN EVPN"  


**1. Фрагмент схемы сети.**

![](Scheme/Scheme.png)


**2. Конфигурация оборудования.**

**POD1 Main**  

Spine1-1 - [Здесь](Configs/Spine1-1.txt)

Spine1-2 - [Здесь](Configs/Spine1-2.txt)

Leaf1-1 -  [Здесь](Configs/Leaf1-1.txt)

Leaf1-2 -  [Здесь](Configs/Leaf1-2.txt)

Leaf1-3 -  [Здесь](Configs/Leaf1-3.txt)

Leaf1-4 -  [Здесь](Configs/Leaf1-4.txt)

**POD2 Reserve**  

Spine2-1 - [Здесь](Configs/Spine2-1.txt)

Spine2-2 - [Здесь](Configs/Spine2-2.txt)

Leaf2-1 -  [Здесь](Configs/Leaf2-1.txt)

Leaf2-2 -  [Здесь](Configs/Leaf2-2.txt)


**3. Вывод и ошибка vPC**  

Пинги до Gw и до другого VPC (L3VPN) проходят через Leaf1-1  
![](CommandResults/Pic2.png)  

Ошибка vPC  
![](CommandResults/Pic3.png)  
![](CommandResults/Pic4.png)
 