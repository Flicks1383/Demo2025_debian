### <div align="center">![image](https://github.com/user-attachments/assets/ebf7c74e-ab37-4d5d-964b-d403e03398f3)
# <div align="center"><strong>МОДУЛЬ 2</strong></div>

<p align="center">
  <img src="https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/%D0%94%D0%B8%D0%B0%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B0%20%D0%B1%D0%B5%D0%B7%20%D0%BD%D0%B0%D0%B7%D0%B2%D0%B0%D0%BD%D0%B8%D1%8F.drawio.png" alt="Топология" />
</p>
<p align="center"><strong>Топология</strong></p>

<br/>

## ❌Задание 1

### Настройте доменный контроллер Samba на машине BR-SRV

- Создайте 5 пользователей для офиса HQ: имена пользователей фомата user№.hq. Создайте группу hq, введите в эту группу созданных пользователей

- Введите в домен машину HQ-CLI

- Пользователи группы hq имеют право аутентифицироваться на клиентском ПК

- Пользователи группы hq должны иметь возможность повышать привилегии для выполнения ограниченного набора команд: cat, grep, id. Запускать другие команды с повышенными привилегиями пользователи не имеют права

- Выполните импорт пользователей из файла users.csv. Файл будет располагаться на виртуальной машине BR-SRV в папке /opt

<br/>

<details>
<summary><strong>[В процессе]</strong></summary>
<br/>

- ___Настрою позже___

</details>

</br>

## ✔️ Задание 2

### Сконфигурируйте файловое хранилище

>[!WARNING]
>#### Перед началом настройки создайте снапшот устройства!!!

- При помощи трех дополнительных дисков, размером 1Гб каждый, на HQ-SRV сконфигурируйте дисковый массив уровня 5

- Имя устройства - md0, конфигурация массива размещается в файле /etc/mdadm.conf

- Обеспечьте автоматическое монтирование в папку /raid5

- Создайте раздел, отформатируйте раздел, в качестве файловой системы используйте ext4

- Настройте сервер сетевой файловой системы (nfs), в качестве папки общего доступа выберите /raid5/nfs, доступ для чтения и записи для всей сети в сторону HQ-CLI

- На HQ-CLI настройте автомонтирование в папку /mnt/nfs

- Основные параметры сервера отметьте в отчете

<br/>

<details>
<summary><strong>[Решение]</strong></summary>
<br/>

</br>

## Конфигурация выполняется на машине HQ-SRV

<br/>

### Добавление дисков на `HQ-SRV` [Если у вас их нету]:

<br/>

**1.** На WEB-морде **EXSI(VMware)** выключаем машину `HQ-SRV` и в настройках машины добавляем **`3 диска`** как показано на изображении:
<p align="center">
  <img src="https://github.com/Flicks1383/Demo2025_debian/blob/main/Module2/addDisk.png" alt="Добавление дисков" width="600" height="400" />
</p>
<br/>

**2.** Далее **запускаем машину** и вводим команду в которой должны отобразиться все диски:

```
lsblk
```

Находим:

> Вывод:
> ```yml
> sdb  8:16  0  1G  0  disk
> sdc  8:32  0  1G  0  disk
> sdd  8:48  0  1G  0  disk
> ```

</br>


**3.** Для начала требуется **установить утилиту**: 

```
apt-get install mdadm
```

<br/>

**2.** После этого **обнуляем суперблоки** командой:

```
mdadm --zero-superblock --force /dev/sd{b,c,d}
```
> Вывод:
> ```yml
> mdadm: Unrecongised md component device - /dev/sdx
> ```
> > Гласит о том, что диски не использовались ранее для **RAID**

**4.** Далее **удаляем метаданные** командой:

```
wipefs --all --force /dev/sd{b,c,d}
```

</br>

**5.** Далее создаем **RAID**:

```
mdadm --create /dev/md0 -l 5 -n 3 /dev/sd{b,c,d}
```
### Проверяем создался ли Raid-массив:
```yml
lsblk
```
> Вывод:
> ```yml
> sdb  8:16  0  1G  0  disk
>   md0  9:0  0  2G  0  raid5
> sdc  8:32  0  1G  0  disk
>   md0  9:0  0  2G  0  raid5
> sdd  8:48  0  1G  0  disk
>   md0  9:0  0  2G  0  raid5
> ```

<br/>

**6.** После чего создаем **файловую систему** командой:  

```
mkfs -t ext4 /dev/md0
```

<br/>


**7.** Создаем **директорию**:  
```
mkdir /etc/mdadm
```

<br/>


**8.** После **заполняем файл** информацией:  
```
echo "DEVICE partitions" > /etc/mdadm/mdadm.conf
mdadm --detail --scan | awk '/ARRAY/ {print}' >> /etc/mdadm/mdadm.conf
```

<br/>


**9.** **Создаем файловую систему** для монтирования массива:  
```
mkdir /mnt/raid5
```

<br/>


**10.** После, в файл **`/etc/fstab`** добавляем строчку:  
```
/dev/md0  /mnt/raid5  ext4  defaults  0  0

ВСЕ ПРОБЕЛЫ СДЕЛАННЫ TAB`ом
```

<br/>

  
**11.** Далее **монтируем** образ командой: **`mount -a`** 

<br/>

❗ **Проверить монтирование массива можно командой: `df -h`**
> Вывод:
> ```yml
> /dev/md0  2.0G  24K  1.9G  1%  /mnt/raid5
> ```
<br/>

## Настройка `NFS` так же производится на `HQ-SRV`:

<br/>

**1.** Устанавливаем **утилиты:**

```
apt-get install -y nfs-{server,utils}
```

</br>

**2.** **Создаем директорию** командой:

```
mkdir /mnt/raid5/nfs
```

</br>

**3.** Задаем **права директории**:  

```
chmod 766 /mnt/raid5/nfs
```

</br>

**4.** В файл **`/etc/exports`** добавляем строку:  

```
/mnt/raid5/nfs 192.168.200.0/28(rw,no_root_squash)
```

</br>

**5.** **Экспорт** файловой системы:

```
exportfs -arv
```

</br>

**6.** Запускаем **NFS сервер** командой: 

```
systemctl enable --now nfs-server
```

</br>

## Далее идет настройка на `HQ-CLI`

**1.**  Устанавливаем NFS клиент:  

```
apt-get update && apt-get install -y nfs-{utils,clients}
```

</br>

**2.** Создаем директорию командой:

```
mkdir /mnt/nfs
```

</br>

**3.** После задаем права:

```
chmod 777 /mnt/nfs
```

</br>

**4.** Добавляем в файл `/etc/fstab` строку:

```
192.168.100.62:/mnt/raid5/nfs  /mnt/nfs  nfs  defaults  0  0

ВСЕ ПРОБЕЛЫ СДЕЛАНЫ TAB`ом
```

**5.** Далее монтируем ресурс командой:
```
mount -a
```

❗ После можно проверить монтирование командой:
  ```
  df -h
  ```
> Вывод:
> ```yml
> 192.168.100.62:/mnt/raid5/nfs  2,0G  0  1,9G  0%  /mnt/nfs
> ```
</details>

</br>


## ✔️ Задание 3

### Настройте службу сетевого времени на базе сервиса chrony

- В качества сервера выступает HQ-RTR

- На HQ-RTR настройте сервер chrony, выберите стратум 5

- В качестве клиентов настройте HQ-SRV, HQ-CLI, BR-RTR, BR-SRV

<br/>

<details>
<summary><strong>[Решение]</strong></summary>
<br/>

## Настройка `chrony` на HQ-RTR

<br/>

**1.** Устанавливаем `chrony` на **HQ-RTR** командой:
```
sudo apt install chrony
```
</br>

**2.** Далее редактируем конфигурационный файл **`sudo nano /etc/chrony/chrony.conf`**

```
#server ntp4.uniiftri.ru iburst <- ПОДОБНЫЕ ЗАПИСИ КОММЕНТИРУЕМ!!!

/// ДОПИСЫВАЕМ ВСЁ ЧТО СНИЗУ ///

server 127.0.0.1 iburst prefer
local stratum 5
allow 192.168.100.0/26
allow 192.168.200.0/28
allow 192.168.0.0/27
```

`server` - машина выступающая на роль сервера chrony;

`iburst` - отправка нескольких пакетов (для точности);

`perfer` - указывает на предпочитаемый сервер;

`local stratum 5` - установка 5 уровня на локальный сервер;

`allow` - устройства с каких подсетей имеют возможность синхронизироваться с сервером;

</br>

**3.** После установки, **перезагружаем сервис** и **добавляем в автозагрузку**:
```
systemctl restart chronyd

systemctl enable --now  chronyd
```

</br>

## Подключение клиентов | Настройка на `HQ-SRV` `HQ-CLI` `BR-RTR` `BR-SRV`

**1.** Устанавливаем пакет **`chrony`**:
```
sudo apt install chrony
```
</br>

**2.** Далее редактируем конфигурационный файл **`sudo nano /etc/chrony/chrony.conf`**
```
#server ntp1.uniiftri.ru iburst <- Комментируем подобные записи в конфиге

server 192.168.100.1 iburst <- Дописываем данную строчку
```
`server 192.168.100.1 iburst` - Указание ip **HQ-RTR** как главный сервер **chrony**

</br>

**3.** После установки, **перезагружаем сервис** и **добавляем в автозагрузку**:
```
systemctl restart chronyd

systemctl enable --now  chronyd
```

## ПРОВЕРКА конфигурации NTP-сервера

  
<details>
  
<summary><strong>[Подробнее]</strong></summary>

</br>

Получаем вывод источников времени с помощью команды:
```yml
chronyc sources
```
> Вывод:
> ```yml
> MS Name/IP address        Stratum  Poll  Reach  LastRx  Last  sample
> =============================================================================
> ^/ localhost.localdomain  0        8     377    -       +0ns[  +0ns] +/-  0ns
> ```

<br/>

Получаем вывод **уровня стратума** с помощью связки команд:
```yml
chronyc tracking | grep Stratum
```
> Вывод:
> ```yml
> Stratum: 5
> ```
</details>

</details>

</br>

# ✔️/❌ Задание 4
## Сконфигурируйте ansible на сервере BR-SRV
- Для начала устранавливаем "Ansible" командой
```
apt-get install ansible -y
```
- В файл `/etc/ansible/hosts.yml` требуется поместить все хосты (пока не знаю каким образом(___разобраться___)):
- Файл `/etc/ansible/inventory.yml` изменить следующим образом:
```
clients:
  hosts:
    hq-cli.au-team.irpo:
servers:
  hosts:
    hq-srv.au-team.irpo:
routers:  
  hosts:
    hq-rtr.au-team.irpo:
    br-rtr.au-team.irpo:  
```
-  Генерация ключа осуществляется командой:
```
ssh-keygen -C "$(whoami)@$(hostname)-$(date -I)"
```
-  Далее требуется распространить ключи на хосты, используя SSH:
```
ssh-copy-id root@server  
```
где:
root - имя пользователя устройства;
server - IP адрес устройства
-  Проверка производится командой:
```
ansible test -m ping
```
# ✔️ Задание 5
## Развертывание приложений в Docker на сервере BR-SRV
- На BR-SRV открываем файл `/home/student/wiki.yml` и приводим к следующему виду:
```
services:
MediaWiki:
   container_name: wiki
   image: mediawiki
   restart: always
   ports: 
     - 80:8080
   links:
     - database
   volumes:
     - images:/var/www/html/images
     # - ./LocalSettings.php:/var/www/html/LocalSettings.php
 database:
   container_name: mariadb
   image: mariadb
   environment:
     MYSQL_DATABASE: mediawiki
     MYSQL_USER: wiki
     MYSQL_PASSWORD: WikiP@ssw0rd
     MYSQL_RANDOM_ROOT_PASSWORD: 'yes'
   volumes:
     - dbvolume:/var/lib/mysql
volumes:
 dbvolume:
     external: true
 images:
```
- После чего устанавливаем Docker на сервер командой:
```
  apt-get install docker-engine
```
-
```
docker compose -f wiki.yml up -d
```
- После сего поднимаем стек контейнеров командой:
```
docker compose -f wiki.yml up -d  
```
- Далее необходимо раскоментировать (убрать #) в ранее сконфигурированом файле
- Далее прописываем команды:
```
docker-compose -f wiki.yml stop  
docker-compose -f wiki.yml up -d  
```
# Задание 6 (Проверить (не уверен в ___работоспособности___))
## На маршрутизаторах сконфигурируйте статическую трансляцию портов
- Для BR-RTR настройка выглядит следующим образом:
```
ip nat source static tcp 192.168.1.2 80 192.168.1.65 8080
ip nat source static tcp 172.16.5.1 80 192.168.1.2 8080
ip nat source static tcp 192.168.1.2 2024 192.168.1.65 2024  

```
- Для HQ-RTR настройка выглядит следующим образом:
```
ip nat source static tcp 192.168.0.2 2024 192.168.1.65 2024  
```
# ❌ Задание 7
## Запустите сервис moodle на сервере HQ-SRV:
# ❌ Задание 8
## Настройте веб-сервер nginx как обратный прокси-сервер на HQ-RTR
- Настройка производится на HQ-RTR в режиме конфигурации:  
# ✔️ Задание 9
## Удобным способом установите приложение Яндекс Браузере для организаций на HQ-CLI
- Если есть встроенный браузер - скачать Яндекс с его помощью
- Если нет - установка при помощи команды:
```
# apt-get install yandex-browser-stable
```
