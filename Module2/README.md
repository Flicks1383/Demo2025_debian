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

После чего требуется прописать команду `systemctl daemon-reload` для следующего шага.
  
**11.** Далее **монтируем** образ командой: **`mount -a`** 

<br/>

❗ **Проверить монтирование массива можно командой: `df -h`**
> Вывод:
> ```yml
> /dev/md0  2.0G  24K  1.9G  1%  /mnt/raid5
> ```
<br/>

**12.** После чего необходимо перезапустить сервер. Если сервер запускается в режиме восстановления, проведите следующие дествия:  
Пропишите команду `lsblk` и запомните цифру, после *md*, после чего перейдите в конфигурацию файла */etc/fstab* и замените цифру
<br/>
> Пример:
> ```yml
> /dev/md127  2.0G  24K  1.9G  1%  /mnt/raid5
> ```
<br/>


## Настройка `NFS` так же производится на `HQ-SRV`:

<br/>

**1.** Устанавливаем **утилиты:**

```
apt-get install -y nfs-server
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
apt-get update && apt-get install -y nfs-client
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
systemctl restart chrony

systemctl enable --now  chrony
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
systemctl restart chrony

systemctl enable --now  chrony
```

## ПРОВЕРКА конфигурации NTP-сервера

  
<details>
  
<summary><strong>[Подробнее]</strong></summary>

</br>

Получаем вывод источников времени с помощью команды:
```yml
chronyc sources
```
> Вывод на сервере:
> ```yml
> MS Name/IP address        Stratum  Poll  Reach  LastRx  Last  sample
> =============================================================================
> ^/ localhost.localdomain     5      8     377     -     +0ns  [+0ns] +/-  0ns
> ```

> Вывод на клиенте:
> ```yml
> MS Name/IP address        Stratum  Poll  Reach  LastRx  Last  sample
> =============================================================================
> ^/ hq-rtr.au-team.irpo     5      6     37       50    +91ns  [+31ns] +/-  88us
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

## ✔️ Задание 4

### Сконфигурируйте ansible на сервере BR-SRV

- Сформируйте файл инвентаря, в инвентарь должны входить HQ-SRV, HQ-CLI, HQ-RTR и BR-RTR

- Рабочий каталог ansible должен располагаться в /etc/ansible

- Все указанные машины должны без предупреждений и ошибок отвечать pong на команду ping в ansible посланную с BR-SRV

<br/>

<details>
<summary><strong>[Решение]</strong></summary>
<br/>

## Настройка ansible производится на `BR-SRV`

<br/>

**1.** Для начала устанавливаем "Ansible" командой:
```
apt-get install ansible -y
```

<br/>


**2.** Создаём пары SSH-ключей следующей командой:

```
ssh-keygen -t rsa
```
- По итогу создания ключей в каталоге пользователя под которым сидим `sshuser` или же `root`, появятся ключи:
  
  - `/home/sshuser/.ssh` - Если зашли за **sshuser**

  - `/root/.ssh` - Если зашли за **root**

>Смотрим каталог с ключами:
>```
>ls -l ~/.ssh
>
>id_rsa  # закрытый ключ
>id_rsa.pub # открытый ключ
>

<br/>

**3.** Заходим под пользователя **`sshuser`**:
```
su sshuser
```

<br/>

**4.** Копируем открытый **`SSH-ключ`** на удаленные устройства под пользователем **`sshuser`**:

- Копируем ключ для пользователя **sshuser** на **`HQ-SRV`**
  - На HQ-SRV ssh порт изменен, указываем его:
```
ssh-copy-id -p 2024 sshuser@192.168.100.62
```

<br/>

- Копируем ключ для пользователя **user** на **`HQ-CLI`**
```
ssh-copy-id user@192.168.200.2
```

<br/>

- Копируем ключ для пользователя **net_admin** на **`HQ-RTR`**
```
ssh-copy-id net_admin@172.16.4.2
```

<br/>

- Копируем ключ для пользователя **net_admin** на **`BR-RTR`**
```
ssh-copy-id net_admin@172.16.5.2
```

<br/>

### Готовим файл инвентаря (hosts)

**1.** Создаем каталог, а также файл инвентаря **`/etc/ansible/demo`**
```
nano /etc/ansible/demo
```

<br/>

**2.** Приводим **файл** в следующий вид:
>```
>[hq]
>192.168.100.62 ansible_port=2024 ansible_user=sshuser
>192.168.200.2 ansible_user=user
>172.16.4.2 ansible_user=net_admin
>
>[br]
>172.16.5.2 ansible_user=net_admin
>```

**где:**
- `ansible_port` - Номер порта ssh, если не 22
- `ansible_user` - Использовать имя пользователя ssh по умолчанию.

<br/>

### Запуск команд с пользовательским инвентарем (ping-pong)

**1.** Что бы запустить модуль ping на всех хостах, перечисленных файле инвентаря **`/etc/ansible/demo`** пишем следующую команду:

```
ansible all -i /etc/ansible/demo -m ping
```

**!!! Может появиться предупреждение про обнаружение интерпретатора Python, на целевом хосте**

<br/>

**2.** Для управления поведением обнаружения в глобальном масштабе необходимо в файле конфигурации **`ansible /etc/ansible/ansible.cfg`** в разделе **`[defaults]`** прописать ключ **`interpreter_python`** с параметром **`auto_silent`**. В большинстве дистрибутивов прописываем вручную.
```
nano /etc/ansible/ansible.cfg

[defaults]
interpreter_python=auto_silent
```
<br/>

**3.** Запускаем команду `ping` на всех хостах:
```
ansible all -i /etc/ansible/demo -m ping
```
<br/>

</details>

<br/>

## ✔️ Задание 5

### Развертывание приложений в Docker на сервере BR-SRV

- Создайте в домашней директории пользователя файл wiki.yml для приложения MediaWiki

- Средствами docker compose должен создаваться стек контейнеров с приложением MediaWiki и базой данных

- Используйте два сервиса

- Основной контейнер MediaWiki должен называться wiki и использовать образ mediawiki

- Файл LocalSettings.php с корректными настройками должен находиться в домашней папке пользователя и автоматически монтироваться в образ

- Контейнер с базой данных должен называться mariadb и использовать образ mariadb

- Разверните

- Он должен создавать базу с названием mediawiki, доступную по стандарнтому порту, пользователя wiki с паролем WikiP@ssw0rd должен иметь права доступа к этой базе данных

- MediaWiki должна быть доступна извне через порт 8080

<br/>

<details>
<summary><strong>[C помощью CLI]</strong></summary>
<br/>

### Установка Wiki (по SSH с CLI на BR-SRV)

**1.** Подключаемся при помощи **HQ-CLI** к **BR-SRV** по `SSH`:
```
ssh sshuser@192.168.0.2 -p2024
```

**2.** Обновляем пакеты и устанавливаем **Docker**:
```
sudo apt update

sudo apt install docker docker-compose docker-doc
```
</br>

**3.**  Добавляем **Docker** в автозагрузку и запускаем:
```
systemctl enable docker --now
```
</br>

**4.** Проверяем статус запущенной службы **(Docker)** и информацию:
```
systemctl status docker

docker info
```

</br>

**5.**  При помощи `CLI` заходим в **YandexBrowser**:

`1 ->` Пишем в поисковик **mediawiki docker-compose**

`2 ->` заходим на сайт [mediawiki.org]

`3 ->` СЛЕВА находим надпись и заходим в ***Adding a Database Server**

`4 ->` копируем конфиг, который там будет.

</br>

**6.** В домашней директории пользователя **sshuser** создаем композер-файл **wiki.yaml**:
```
cd /home/sshuser

nano wiki.yaml
```

</br>

**8.** Копируем и вставляем содержимое c сайта в **wiki.yml**:

 <img src="https://github.com/Flicks1383/Demo2025_debian/blob/main/Module2/WIKItutorial.png" alt="Конфиг" width="400" height="400" />

</br>


**9.** Чтобы отдельный **volume** для хранения базы данных **имел правильное имя** - создаём его средствами **docker**:
```
sudo docker volume create dbvolume
```

**`Информация|Проверка.`** Посмотреть все тмеющиеся **volume** можно командой:
```
sudo docker volume ls
```
</br>

**10.** Выполняем сборку и запуск стека контейнеров с приложением **MediaWiki** и базой данных описанных в файле **wiki.yml**:
```
sudo docker-compose -f wiki.yaml up -d
```
</br>

### Настройка Wiki через WEB-интерфейс:

**1.** Переходим на `HQ-CLI` в браузере по адресу **http://192.168.0.2:8080** (айпишник BR-SRV:8080):
- Для продолжения установки через **WEB-интерфейс** - нажимаем **`set up the wiki`**
 
  </br>

**2.** Выбираем необходимый Язык - жмем **Далее**, проходим проверку внешней среды и так-же нажимаем **далее**:
</br>

**3.** Заполняем параметры подключение к **БД** в соответствие с заданными переменными окружения в **wiki.yml**, которые соответствуют заданию:

 ![image](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module2/wiki.png)

</br>

---

**4.** Ставим галочку и жмем **Далее**:

</br>

---

**5.** Вносим необхоимые изменения, ставим галочку и жмём **Далее**:

![image](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module2/wiki%202.png)

</br>

**6.** Будет автоматически скачен файл **`LocalSettings.php`** - который необходимо передать на **BR-SRV** c HQ-CLI в директорию **`/home/sshuser`** туда же где лежит **`wiki.yaml`**:
```
scp -P 2024 /home/user/Загрузки/LocalSettings.php sshuser@192.168.0.2:/home/sshuser
```
</br>

**7.** Раскомментируем строку в файле **`wiki.yaml`** :
```
nano /home/sshuser/wiki.yaml
```
![image](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module2/phplocalconfigwiki.png)

</br>

**8.** Перезапускаем сервисы средствами **`docker-compose`**:
```
docker-compose -f wiki.yaml stop

docker-compose -f wiki.yaml up -d
```
</br>

**9.** Проверяем доступ к Wiki **`http://192.168.0.2:8080`**

Входим под

- `Пользователь`: wiki 

- `Пароль`: WikiP@ssw0rd

</br>

</details>

## ✔️ Задание 6

### На маршрутизаторах сконфигурируйте статическую трансляцию портов

>[!WARNING]
>Настройка портов проходит с помощью **[IPTABLES](https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/README.md#%EF%B8%8F-задание-2 "Установка IPtables")**

- Пробросьте порт 80 в порт 8080 на BR-SRV на маршрутизаторе BR-RTR, для обеспечения работы сервиса wiki
  
- Пробросьте порт 2024 в порт 2024 на HQ-SRV на маршрутизаторе HQ-RTR
  
- Пробросьте порт 2024 в порт 2024 на BR-SRV на маршрутизаторе BR-RTR

<br/>

<details>
<summary><strong>[Решение]</strong></summary>
<br/>

### BR-RTR

**1.** Проброс **80** порта и **2024** для BR-SRV
```
### Проброс порта 80 на порт 8080 на BR-SRV
sudo iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to-destination 192.168.0.2:8080

### Разрешение трафика на **BR-SRV**
sudo iptables -A FORWARD -p tcp -d 192.168.0.2 --dport 8080 -m state --state NEW,ESTABLISHED,RELATED -j ACCEPT

######################################

### Проброс порта 2024 на BR-SRV
sudo iptables -t nat -A PREROUTING -p tcp --dport 2024 -j DNAT --to-destination 192.168.0.2:2024

# Разрешение трафика на BR-SRV
sudo iptables -A FORWARD -p tcp -d 192.168.0.2 --dport 2024 -m state --state NEW,ESTABLISHED,RELATED -j ACCEPT
```
<br/>

### HQ-RTR

**2.** Проброс порта **2024** для HQ-SRV
```
### Проброс порта 2024 на HQ-SRV
sudo iptables -t nat -A PREROUTING -p tcp --dport 2024 -j DNAT --to-destination 192.168.100.62:2024

### Разрешение трафика на HQ-SRV
sudo iptables -A FORWARD -p tcp -d 192.168.100.62 --dport 2024 -m state --state NEW,ESTABLISHED,RELATED -j ACCEPT
```
<br/>

</details>

<br/>

## ✔️ Задание 7 (Тестируется)

### Запустите сервис moodle на сервере HQ-SRV:

  - Используйте веб-сервер apache
 
  - В качестве системы управления базами данных используйте mariadb
  
  - Создайте базу данных moodledb
  
  - Создайте пользователя moodle с паролем P@ssw0rd и предоставьте ему права доступа к этой базе данных
  
  - У пользователя admin в системе обучения задайте пароль P@ssw0rd
  
  - На главной странице должен отражаться номер рабочего места в виде арабской цифры, других подписей делать не надо
    
  - Основные параметры отметьте в отчёте

<details>
<summary><strong>[Решение]</strong></summary>
<br/>

### HQ-SRV

**1.** Устанавливаем необходимые **пакеты**:
```
sudo apt update

sudo apt install -y apache2 mariadb-server mariadb-client php php-mysql libapache2-mod-php php-xml php-mbstring php-zip php-curl php-gd git
```
</br>

**2.** Запуcкаем **MariaDB**:
```
sudo systemctl start mariadb

sudo systemctl enable mariadb
```

</br>

**3.** Зайдите в консоль **MariaDB**:
```
sudo mysql -u root -p
```
</br>

**4.** **Создайте** базу данных и пользователя:
```
CREATE DATABASE moodledb DEFAULT CHARACTER SET utf8;
CREATE USER 'moodle'@'localhost' IDENTIFIED BY 'P@ssw0rd';
GRANT ALL ON moodledb.* TO 'moodle'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```
</br>

**`НЕОБЯЗАТЕЛЬНО`**

**Настройка безопасности** Mariadb
  <details>
  <summary><strong>[Подробнее]</strong></summary>
  </br>

  **1.** Запуск скрипта безопасности:
  ```
  sudo mysql_secure_installation
  ```
  </br>

  **2.** После запуска скрипта вам будет предложено ответить на несколько вопросов. Вот что вам нужно будет сделать:

  - **`Введите текущий пароль для пользователя root`**: Если вы только что установили MariaDB и не устанавливали пароль, просто нажмите **Enter**.

  - **`Установить пароль для пользователя root?`**: Если вы хотите установить пароль для пользователя **root**, выберите **Y (Yes)** и **введите новый пароль**. **Рекомендуется использовать сложный пароль**.

  - **`Удалить анонимных пользователей?`**: Выберите **Y (Yes)**, **чтобы удалить анонимных пользователей**. Это **повысит безопасность**.

  - **`Запретить удаленный доступ к пользователю root?`**: Выберите **Y (Yes)**, чтобы **запретить удаленный доступ к пользователю root**. Это также повысит безопасность.

  - **`Удалить тестовую базу данных и доступ к ней?`**: Выберите **Y (Yes)**, чтобы **удалить тестовую базу данных**. Это предотвратит доступ к ней.

  - **`Перезагрузить таблицы привилегий?`**: Выберите **Y (Yes)**, чтобы **перезагрузить таблицы привилегий**, чтобы **изменения вступили в силу**.

  </br>
  
  </details>
  </br>

**5.** Перезапускаем MariaDB:
```
systemctl restart mariadb-server
```

## Установка *Moodle* через *Git*

### HQ-SRV

**5.** Заходим в директорию где будет установлен **moodle**
```
cd /tmp
```
</br>

**6.** Клонируем репозиторий **Moodle**:
```
git clone git://git.moodle.org/moodle.git
```
</br>

**7.** Переходим в директорию **Moodle**
```
cd moodle
```
</br>

**8.** Чтобы узнать, какая версия является последней доступной, выполните:
```
cd moodle
git tag
```
</br>

**9.** Клонирование репозитория **Moodle**
```
sudo git checkout -t origin/MOODLE_405_STABLE
                                    ^ данная версия актуальна в момент написания методички, проверяйте версию в git tag
```
</br>

**10.** Настройка директорий и прав:
```
sudo mkdir -p /var/www/html/moodle
sudo mkdir -p /var/www/moodledata
sudo chown -R www-data:www-data /var/www/moodledata
sudo chmod -R 770 /var/www/moodledata
sudo chown -R www-data:www-data /var/www/html/moodle
```
**11.** Далее переносим файлы в другой каталог
mv -f /tmp/moodle/{.,}* /var/www/html/moodle

</br>

**12.** Создание файла конфигурации **Apache**
```
sudo nano /etc/apache2/sites-available/moodle.conf
```
</br>

**13.** Вставляем следующие настройки в эту конфигурацию:
```
<VirtualHost *:80>
    ServerAdmin hq-srv@au-team.irpo
    DocumentRoot /var/www/html/moodle/
    DirectoryIndex index.php
    <Directory /var/www/html/moodle/>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/moodle_error.log
    CustomLog ${APACHE_LOG_DIR}/moodle_access.log combined
</VirtualHost>
```
</br>

**14.** Активируем новый сайт и модули:
```
sudo a2ensite moodle.conf
sudo a2enmod rewrite
```
</br>

**15.** Перезапускаем **Apache**:
```
sudo systemctl restart apache2
```
</br>

## Настройка в Moodle в Web-интерфейсе

**1.** Откройте веб-браузер и перейдите по адресу http://192.168.100.62.

**2.** В процессе установки укажите данные для подключения к базе данных:

**`База данных`**: moodledb

**`Пользователь`**: moodle

**`Пароль`**: P@ssw0rd

</details>
</br>

## ✔️ Задание 8

### Настройте веб-сервер nginx как обратный прокси-сервер на HQ-RTR

- При обращении к HQ-RTR по доменному имени moodle.au-team.irpo клиента должно перенаправлять на HQ-SRV на стандартный порт, на сервис moodle

- При обращении к HQ-RTR по доменному имени wiki. au-team.irpo клиента должно перенаправлять на BR-SRV на порт, на сервис mediawiki
<br/>

<details>
<summary><strong>[Решение]</strong></summary>
<br/>

**1.** Установка **Nginx**:
```
sudo apt install nginx -y
```

**2.** Включаем автозагрузку службы:
```
systemctl enable --now nginx
```

**3.** Открываем на редактирование конфигурационный файл **`Nginx`**
```
nano nano /etc/nginx/nginx.conf
```

**4.** Cпускаемся в конец документа и перед последней фигурной скобкой **`}`** прописываем:
```
server  {
        listen 80;
        server_name moodle.au-team.irpo;

        location / {
            proxy_pass http://192.168.100.62:80;
        }
}

server {
        listen 80;
        server_name wiki.au-team.irpo;

        location / {
            proxy_pass http://192.168.0.2:8080;
        }
}
```
</br>

### ПРОВЕРКА

- На **`HQ-CLI`** в браузере заходим по доменному имени:

  на **`Moodle`** – moodle.au-team.irpo

  на **`MediaWiki`** – wiki.au-team.irpo


</br>

**5.** Перезагружаем **`Nginx`**
```
systemctl restart nginx
```

</details>
  
## ✔️ Задание 9

### Удобным способом установите приложение Яндекс Браузере для организаций на HQ-CLI

- Установку браузера отметьте в отчёте
<br/>

<details>
<summary><strong>[Решение]</strong></summary>
<br/>

`Если есть встроенный браузер` - скачать Яндекс с его помощью

`Если нет` - установка при помощи **команды**:

```
# sudo apt-get install yandex-browser-stable
```

</details>
