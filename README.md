# <div align="center"><strong>Демонстрационный экзамен (Debian)</strong></div>
### <div align="center"><strong>Описание</strong> </div>
<div align="center">Инструкция для написания демонстрационного экзамена 2025 года для специальности</div> <div align="center"><strong>СЕТЕВОЕ И СИСТЕМНОЕ АДМИНИСТРИРОВАНИЕ 09.02.06</strong></div>
</br>

>[!WARNING]
>Убедительная просьба, перед тем как начинать настраивать машины, **ДЕЛАЙТЕ СНАПШОТИКИ**, для отката после каждого выполненного задания !!!

>[!WARNING]
>Так же не забывайте про **`sysctl -p`** на Роутерах!!!

</br>

## Модули и их решения: 

+ **[⚙️МОДУЛЬ 1](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/README.md)** 
<details>
  <summary><ins>Содержание</ins></summary> 
  
  1. **[Произведите _базовую настройку_ устройств](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/README.md#%EF%B8%8F-задание-1)**
  
  2. **[Настройка _ISP_](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/README.md#%EF%B8%8F-задание-2)**
  
  3. **[Создание _ЛОКАЛЬНЫХ_ учетных записей](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/README.md#%EF%B8%8F-задание-3)**
  
  4. **[Настройте на интерфейсе _HQ-RTR_ в сторону офиса _HQ_ виртуальный коммутатор](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/README.md#-задание-4)** (В процессе)
   
  5. **[Настройка безопасного удаленного доступа на серверах _HQ-SRV_ и _BR-SRV_](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/README.md#%EF%B8%8F-задание-5)**
  
  6. **[Между офисами _HQ_ и _BR_ необходимо сконфигурировать _IP-туннель_](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/README.md#%EF%B8%8F-задание-6)**

  7. **[Обеспечьте _ДИНАМИЧЕСКУЮ МАРШРУТИЗАЦИЮ_](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/README.md#%EF%B8%8F-задание-7)**

  8. **[Настройка _ДИНАМИЧЕСКОЙ ТРАНСЛЯЦИИ АДРЕСОВ_](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/README.md#%EF%B8%8F-задание-8)**

  9. **[Настройка _ПРОТОКОЛА ДИНАМИЧЕСКОЙ КОНФИГУРАЦИИ ХОСТОВ_](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/README.md#%EF%B8%8F-задание-9)**

  10. **[Настройка _DNS для офисов HQ и BR_](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/README.md#%EF%B8%8F-задание-10)**

  11. **[Настройте _ЧАСОВОЙ ПОЯС_ на всех устройствах, согласно месту проведения экзамена](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/README.md#%EF%B8%8F-задание-11)**
    
  </details>

</br>

+ **[⚙️МОДУЛЬ 2](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module2/README.md#модуль-2)**
<details>
  <summary><ins>Содержание</ins></summary>

1. **[Настройте доменный контроллер _SAMBA_ на машине _BR-SRV_](https://github.com/Flicks1383/Demo2025_debian/tree/main/Module2#задание-1)**
    
2. **[Сконфигурируйте _ФАЙЛОВОЕ ХРАНИЛИЩЕ_](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module2/README.md#%EF%B8%8F-задание-2)**

3. **[Настройте службу сетевого времени на базе сервиса _CHRONY_](https://github.com/Flicks1383/Demo2025_debian/tree/main/Module2#%EF%B8%8F-задание-3)**

4. **[Сконфигурируйте _ANSIBLE_ на сервере BR-SRV]()**
    
5. **[Развертывание приложений в _DOCKER_ на сервере BR-SRV]()**
    
6. **[На маршрутизаторах сконфигурируйте _СТАТИЧЕСКУЮ ТРАНСЛЯЦИЮ ПОРТОВ_]()**

7. **[Запустите сервис _MOODLE_ на сервере _HQ-SRV_:]()**

8. **[Настройте веб-сервер _NGINX_ как обратный _ПРОКСИ-СЕРВЕР_ на _HQ-RTR_]()**

9. **[Удобным способом установите приложение _Яндекс Браузере_ для организаций на _HQ-CLI_]()**
  </details>

</br>

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

