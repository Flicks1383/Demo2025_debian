### <div align="center">![image](https://github.com/user-attachments/assets/ebf7c74e-ab37-4d5d-964b-d403e03398f3)
# <div align="center"><strong>МОДУЛЬ 2</strong></div>

<p align="center">
  <img src="https://github.com/Flicks1383/Demo09.02.06_2025/blob/main/module1/Диаграмма%20без%20названия.jpg?raw=true" alt="Топология" />
</p>
<p align="center"><strong>Топология</strong></p>

# Задание 1
## Настройте доменный контроллер Samba на машине BR-SRV
- ___Настрою позже___
# Задание 2
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
# Задание 3
## Настройте службу сетевого времени на базе сервиса chrony
- Настройка производится на HQ-RTR
```
ntp server 172.16.14.1 5  
  ntp timezone Asia/Tomsk (возможны изменения)  
  ntp timezone UTC+7
wr mem
```
# Задание 4
## Сконфигурируйте ansible на сервере BR-SRV
- Для начала устранавливаем "Ansible" командой
```
dnf install ansible -y
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
# Задание 5
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
# Задание 7
## Запустите сервис moodle на сервере HQ-SRV:
# Задание 8
## Настройте веб-сервер nginx как обратный прокси-сервер на HQ-RTR
- Настройка производится на HQ-RTR в режиме конфигурации:
```
 filter-map policy ipv4 moodle 1
 match 80 172.16.4.1/28 192.168.0.2/26 dscp 0
 set redirect hq-rtr.moodle.au-team.irpo
 redirect-url SITEREDIRECT
 url hq-rtr.moodle.au-team.irpo
 end
 wr mem
```
# Задание 9
## Удобным способом установите приложение Яндекс Браузере для организаций на HQ-CLI
- Если есть встроенный браузер - скачать Яндекс с его помощью
- Если нет - установка при помощи команды:
```
# apt-get install yandex-browser-stable
```
