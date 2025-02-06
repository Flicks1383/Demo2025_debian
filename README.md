# <div align="center"><strong>Демонстрационный экзамен (Debian+EcoRouter)</strong></div>
### <div align="center"><strong>Описание</strong> </div>
<div align="center">Инструкция для написания демонстрационного экзамена 2025 года для специальности</div> <div align="center"><strong>СЕТЕВОЕ И СИСТЕМНОЕ АДМИНИСТРИРОВАНИЕ 09.02.06</strong></div>
</br>

>[!WARNING]
>Убедительная просьба, перед тем как начинать настраивать машины, **ДЕЛАЙТЕ СНАПШОТИКИ**, для отката после каждого выполненного задания !!!

>[!WARNING]
>Так же не забывайте про **`wr mem`** на EcoRouter !!!

</br>

## Модули и их решения: 

+ **[⚙️МОДУЛЬ 1]()** 
<details>
  <summary><ins>Содержание</ins></summary> 
  
  1. **[Произведите _базовую настройку_ устройств]()**
  
  2. **[Настройка _ISP_]()**
  
  3. **[Создание _ЛОКАЛЬНЫХ_ учетных записей]()**
  
  4. **[Настройте на интерфейсе _HQ-RTR_ в сторону офиса _HQ_ виртуальный коммутатор]()**
   
  5. **[Настройка безопасного удаленного доступа на серверах _HQ-SRV_ и _BR-SRV_]()**
  
  6. **[Между офисами _HQ_ и _BR_ необходимо сконфигурировать _IP-туннель_]()**

  7. **[Обеспечьте _ДИНАМИЧЕСКУЮ МАРШРУТИЗАЦИЮ_]()**

  8. **[Настройка _ДИНАМИЧЕСКОЙ ТРАНСЛЯЦИИ АДРЕСОВ_]()**

  9. **[Настройка _ПРОТОКОЛА ДИНАМИЧЕСКОЙ КОНФИГУРАЦИИ ХОСТОВ_]()**

  10. **[Настройка _DNS для офисов HQ и BR_]()**

  11. **[Настройте _ЧАСОВОЙ ПОЯС_ на всех устройствах, согласно месту проведения экзамена]()**
    
  </details>

</br>

+ **[⚙️МОДУЛЬ 2]()**
<details>
  <summary><ins>Содержание</ins></summary>

1. **[Настройте доменный контроллер _SAMBA_ на машине _BR-SRV_]()**
    
2. **[Сконфигурируйте _ФАЙЛОВОЕ ХРАНИЛИЩЕ_]()**

3. **[Настройте службу сетевого времени на базе сервиса _CHRONY_]()**

4. **[Сконфигурируйте _ANSIBLE_ на сервере BR-SRV]()**
    
5. **[Развертывание приложений в _DOCKER_ на сервере BR-SRV]()**
    
6. **[На маршрутизаторах сконфигурируйте _СТАТИЧЕСКУЮ ТРАНСЛЯЦИЮ ПОРТОВ_]()**

7. **[Запустите сервис _MOODLE_ на сервере _HQ-SRV_:]()**

8. **[Настройте веб-сервер _NGINX_ как обратный _ПРОКСИ-СЕРВЕР_ на _HQ-RTR_]()**

9. **[Удобным способом установите приложение _Яндекс Браузере_ для организаций на _HQ-CLI_]()**
  </details>

</br>

## Решения проблем

- ### Не пингуются машины между разными **подсетями**
**РЕШЕНИЕ:**
1. Убедитесь, что вся `адресация сети` выполнена точно по методическим материалам, и пингует соседние уст-ва.
2. При использовании `nmtui` в конфигурационном файле `/etc/network/interfaces` не должно быть никаких изменений.
3. Проверьте конфигурацию FRR маршрутизаторов HQ и BR и убедитесь, что все сети видят соседей при помощи команд:
   ```
   show ip ospf neighbor
   show ip route ospf
   ```
4. Если есть снапшоты уст-ва, где сеть всё ещё работала, рекомендую откатиться и произвести выполнение задания снова, внимательно.


