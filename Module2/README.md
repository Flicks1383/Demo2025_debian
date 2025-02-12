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

## Сконфигурируйте файловое хранилище
- Для начала требуется установить утилиту: `apt-get install mdadm`
- После чего необходимо узнать имена дисков командой: `lsblk`
- После этого обнуляем суперблоки командой:  
```
mdadm --zero-superblock --force /dev/sd{b,c,d}
```
- Далее удаляем метаданные командой:  
```
wipefs --all --force /dev/sd{b,c,d}
```
- Далее создаем RAID:  
```
mdadm --create /dev/md0 -l 5 -n 3 /dev/sd{b,c,d}
```
- Можно ввести команду `lsblk` и увидеть массив
- После чего создаем файловую систему командой:  
```
mkfs -t ext4 /dev/md0
```
- Создаем директорию:  
```
mkdir /etc/mdadm
```
- После заполняем файл информацией:  
```
echo "DEVICE partitions" > /etc/mdadm/mdadm.conf
mdadm --detail --scan | awk '/ARRAY/ {print}' >> /etc/mdadm/mdadm.conf
```
- Создаем файловую систему для монтирования массива:  
```
mkdir /mnt/raid5
```
- После, в файл `/etc/fstab` добавляем строчку:  
```
/dev/md0  /mnt/raid5  ext4  defaults  0  0
```  
- Далее монтируем образ командой: `mount -a`  
- Проверить монтирование массива можно командой: `df -h`  
## Настройка NFS:  
- Устанавливаем утилиты:
```
apt-get install -y nfs-{server,utils}
```
- Создаем директорию командой:
```
mkdir /mnt/raid5/nfs
```
- Задаем права директории:  
```
chmod 766 /mnt/raid5/nfs
```
- В файл `/etc/exports` добавляем строку:  
```
/mnt/raid5/nfs 192.168.200.0/28(rw,no_root_squash)
```
- Экспорт файловой системы:
```
exportfs -arv
```
- Запускаем NFS сервер командой:  
```
systemctl enable --now nfs-server
```
## Далее идет настройка на HQ-CLI
- Устанавливаем NFS клиент:  
```
apt-get update && apt-get install -y nfs-{utils,clients}
```
- Создаем директорию командой:
```
mkdir /mnt/nfs
```
- После задаем права:
```
chmod 777 /mnt/nfs
```
- Добавляем в файл `/etc/fstab` строку:
```
192.168.100.62:/mnt/raid5/nfs  /mnt/nfs  nfs  defaults  0  0
```
- Далее монтируем ресурс командой: `mount -a`
- После можно проверить монтирование командой: `df -h`

</details>

# ❌ Задание 3
## Настройте службу сетевого времени на базе сервиса chrony
- Настройка производится на HQ-RTR
```
ntp server 172.16.14.1 5  
  ntp timezone Asia/Tomsk (возможны изменения)  
  ntp timezone UTC+7
wr mem
```
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
