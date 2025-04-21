# <div align="center"><strong>Демонстрационный экзамен (Debian)</strong></div>
### <div align="center"><strong>Описание</strong> </div>
<div align="center">Инструкция для написания демонстрационного экзамена 2025 года для специальности</div> <div align="center"><strong>СЕТЕВОЕ И СИСТЕМНОЕ АДМИНИСТРИРОВАНИЕ 09.02.06</strong></div>
</br>

>[!CAUTION]
>Вся инструкция сделана на конфигурации **`/etc/network/interfaces`**

>[!CAUTION]
>Делаем **`СНАПШОТЫ`** перед настройкой чего то глобального !!!

>[!WARNING]
>**`sysctl -p`** на Роутерах !!!

<details>
  <summary><strong>[если много времени то: sysctl -p при перезагрузке]</strong></summary> 
  
>```
>echo net.ipv4.ip_forward=1 > /etc/sysctl.conf
>
>sudo tee /etc/systemd/system/sysctl-p.service > /dev/null <<EOF
>
>[Unit]
>Description=Apply sysctl settings
>After=network.target
>
>[Service]
>Type=oneshot
>ExecStart=/sbin/sysctl -p
>
>[Install]
>WantedBy=multi-user.target
>EOF
>
>sudo systemctl daemon-reload
>sudo systemctl enable sysctl-p.service
>sudo systemctl start sysctl-p.service
>```
</details>

>[!WARNING]
>После конфигурации **`/etc/network/interfaces`** пишите команду **`systemctl restart networking`** для применения параметров

</br>

## Методический материал: 

<strong>[**МЕТОДИЧКА** с moodle](https://github.com/Flicks1383/Demo2025_debian/blob/main/NGINX.pdf)<strong>

</br>

## Модули и их решения: 

</br>

**[⚙️МОДУЛЬ 1](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/README.md "Удачи!")** 
<details>
  <summary><strong>[Содержание]</strong></summary> 
  
  1. [_**`БАЗОВАЯ`**_ настройка](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/README.md#%EF%B8%8F-задание-1)
  
  2. [Настройка _**`NAT`**_ на _**`ISP`**_](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/README.md#%EF%B8%8F-задание-2)
  
  3. [Как создать _**`SSHUSER`**_ и _**`NET_ADMIN`**_](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/README.md#%EF%B8%8F-задание-3)
  
  4. [Создание _**`VLAN`**_ на **`HQ-RTR`**](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/README.md#-задание-4)
   
  5. [Настройка _**`SSH`**_  **`HQ-SRV`** и **`BR-SRV`**](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/README.md#%EF%B8%8F-задание-5)
  
  6. [_**`IP-туннель`**_ между офисами _**`HQ`**_ и _**`BR`**_](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/README.md#%EF%B8%8F-задание-6)

  7. [Настройка _**`OSPF`**_](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/README.md#%EF%B8%8F-задание-7)

  8. [Настройка __**`NAT`**_ на **`HQ-rtr`** и **`BR-rtr`**](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/README.md#%EF%B8%8F-задание-8)

  9. [Настройка _**`DHCP`**_](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/README.md#%EF%B8%8F-задание-9)

  10. [Настройка _**`DNS`**_ для офисов HQ и BR](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/README.md#%EF%B8%8F-задание-10)

  11. [Настройка _**`ЧАСОВОГО ПОЯСЯ`**_ на всех устройствах, согласно месту проведения экзамена](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/README.md#%EF%B8%8F-задание-11)
    
  </details>

</br>

**[⚙️МОДУЛЬ 2](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module2/README.md#модуль-2 "Ну с богом!")**
<details>
 <summary><strong>[Содержание]</strong></summary> 

1. [Настройка _**`SAMBA`**_ на _BR-SRV_](https://github.com/Flicks1383/Demo2025_debian/tree/main/Module2#задание-1) - ТЕСТИРУЕТСЯ
    
2. [Конфигурация _**`RAID + NFS`**_](https://github.com/Flicks1383/Demo2025_debian/tree/main/Module2#%EF%B8%8F-задание-2-тестируется)

3. [Настройка _**`CHRONY`**_](https://github.com/Flicks1383/Demo2025_debian/tree/main/Module2#%EF%B8%8F-задание-3-тестируется)

4. [Конфигурация _**`ANSIBLE`**_ на BR-SRV](https://github.com/Flicks1383/Demo2025_debian/tree/main/Module2#%EF%B8%8F-задание-4---тестируется) - ТЕСТИРУЕТСЯ
    
5. [Конфигурация **`DOCKER + WIKI`**_ на  BR-SRV](https://github.com/Flicks1383/Demo2025_debian/tree/main/Module2#%EF%B8%8F-задание-5-тестируется)
    
6. [Проброс _**`ПОРТОВ`**_](https://github.com/Flicks1383/Demo2025_debian/tree/main/Module2#%EF%B8%8F-задание-6-тестируется)

7. [Запуск _**`MOODLE`**_ на _HQ-SRV_:](https://github.com/Flicks1383/Demo2025_debian/tree/main/Module2#%EF%B8%8F-задание-7-тестируется)

8. [Настройка прокси_**`NGINX`**_ на _HQ-RTR_](https://github.com/Flicks1383/Demo2025_debian/tree/main/Module2#%EF%B8%8F-задание-8)

9. [Установка _**`Яндекс Браузера`**_ на _HQ-CLI_](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module2/README.md#%EF%B8%8F-задание-9)
  </details>

</br>

### Репозитории добряков откуда взят материал:
+ **>[damh66](https://github.com/damh66/demo2025)**
+ **>[ItsLiventsev](https://github.com/ItsLiventsev/NetSys_Demo_2025?tab=readme-ov-file)**
+ **>[abdurrah1m](https://github.com/abdurrah1m/DEMO2025/blob/main/README.md)**
+ **>[doughboyjaba](https://github.com/doughboyjaba/demo25)**
+ **>[каб-220](http://каб-220.рф/ru/demo-2025/modul-1/modul-1-1)**

## Решения проблем


### Нету `Ping` между устройствами:

<details>
  <summary><ins><strong>[Решение]</strong></ins></summary> 
  </br>
  
**1.** Сверяем порты с **MAC-адресами** соседних **уст-в**, при этом проверяя **правильно ли настроена адресация сети**.

</br>

**2.** При использовании **`nmtui`** в конфигурационном файле `/etc/network/interfaces` не должно быть никаких лишних записей.
>Что должно быть:
>```
>auto lo
>iface lo inet loopback
>```

</br>

**3.** Проверьте конфигурацию **FRR** маршрутизаторов **HQ** и **BR** убедившись, что все сети видят соседей при помощи команд:
   ```
   show ip ospf neighbor
   show ip route ospf
   ```

</br>


**4.** Если есть снапшоты уст-ва, где сеть всё ещё работало, рекомендую откатиться и произвести выполнение задания снова, внимательно.

</br>


**5.** Включение форварда пакетов:
```
sysctl -p net.ipv4.ip_forward=1
```
  </br>
  
</details>

# Временно не решеные проблемы:
❌ SAMBA;
❌ Ansible
