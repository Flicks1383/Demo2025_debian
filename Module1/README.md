# <div align="center"><strong>⚙️</strong></div> <div align="center"><strong>МОДУЛЬ 1</strong></div>

<br/>

<p align="center">
  <img src="https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/Диаграмма%20без%20названия.drawio.png" alt="Топология" />
</p>

### <p align="center"><strong>Топология</strong></p>

<br/>

> [!NOTE]
> Для удобства проверки адресации можно использовать:
>
> ```
> ip -c -br a \\\ кратко - четко
> 
> ip -c a \\\ где мой серый мир
> ```

## ✔️ Задание 1
### Произведите базовую настройку устройств

> [!WARNING]
> **При использовании `nmtui` для сетевой настройки ни в коем случае не редактируйте `/etc/network/interfaces`, иначе отключите сервис `nmtui`, так как возникает конфликт между двумя сервисами настройки сети.**

> [!NOTE]
> **По ходу выполнения заданий, можно воспользоваться сразу же редактором файла NANO**

- Настройте имена устройств согласно топологии. Используйте полное доменное имя
- На всех устройствах необходимо сконфигурировать IPv4
- IP-адрес должен быть из приватного диапазона, в случае, если сеть локальная, согласно RFC1918
- Локальная сеть в сторону HQ-SRV(VLAN100) должна вмещать не более 64 адресов
- Локальная сеть в сторону HQ-CLI(VLAN200) должна вмещать не более 16 адресов
- Локальная сеть в сторону BR-SRV должна вмещать не более 32 адресов
- Локальная сеть для управления(VLAN999) должна вмещать не более 8 адресов
- Сведения об адресах занесите в отчёт, в качестве примера используйте Таблицу 3
<br/>
<details>
<summary><strong>Настройка при помощи *nmtui*</strong></summary>
<br/>
  
   ## > Настройка имени устройств <
   
  - Для **Linux** используется команда `hostnamectl set-hostname (имя устройства.au-team.irpo)`
    
  - Для **EcoRouter** используется команда `hostname (имя устройства)`
    
>ISP: `isp.au-team.irpo`
>
>HQ-RTR: `hq-rtr.au-team.irpo`
>
> BR-RTR: `br-rtr.au-team.irpo`
>
> HQ-SRV: `hq-srv.au-team.irpo`
>
> HQ-CLI: `hq-cli.au-team.irpo`
>
> BR-SRV: `br-srv.au-team.irpo`

#

### <p align="center"><strong>Таблицы адрессации </strong></p>

<br/>

<table align="center">
  <tr>
    <td align="center"><strong>Сеть</strong></td>
    <td align="center"><strong>Адрес подсети</strong></td>
    <td align="center"><strong>Пул-адресов</strong></td>
  </tr>
  <tr>
    <td align="center">SRV-Net (VLAN 200)</td>
    <td align="center">192.168.200.0/26</td>
    <td align="center">192.168.200.1 - 62</td>
  </tr>
  <tr>
    <td align="center">CLI-Net (VLAN 100)</td>
    <td align="center">192.168.100.0/28</td>
    <td align="center">192.168.100.1 - 14</td>
  </tr>
  <tr>
    <td align="center">BR-Net</td>
    <td align="center">192.168.0.0/27</td>
    <td align="center">192.168.0.1 - 30</td>
  </tr>
  <tr>
    <td align="center">MGMT (VLAN 999)</td>
    <td align="center">192.168.99.0/29</td>
    <td align="center">192.168.99.1 - 6</td>
  </tr>
  <tr>
    <td align="center">ISP-HQ</td>
    <td align="center">172.16.4.0/28</td>
    <td align="center">172.16.4.1 - 14</td>
  </tr>
  <tr>
    <td align="center">ISP-BR</td>
    <td align="center">172.16.5.0/28</td>
    <td align="center">172.16.5.1 - 14</td>
  </tr>
</table>
<p align="center"><strong>Таблица подсетей</strong></p>

# <br/>

<table align="center">
  <tr>
    <td align="center"><strong>Устройство</strong></td>
    <td align="center"><strong>Интерфейс</strong></td>
    <td align="center"><strong>IPv4/IPv6</strong></td>
    <td align="center"><strong>Маска/Префикс</strong></td>
    <td align="center"><strong>Шлюз</strong></td>
    <td align="center"><strong>Сеть</strong></td>
  </tr>
  <tr>
    <td align="center" rowspan="3">ISP</td>
      <td align="center">ens192</td>
    <td align="center">192.168.###.### (DHCP)</td>
    <td align="center">/##</td>
    <td align="center">192.168.###.###</td>
    <td align="center">INTERNET</td>
  </tr>
  <tr>
    <td align="center">ens161</td>
    <td align="center">172.16.4.1</td>
    <td align="center">/28</td>
    <td align="center"></td>
    <td align="center">ISP-HQ-RTR</td>
  </tr>
  <tr>
    <td align="center">ens224</td>
    <td align="center">172.16.5.1</td>
    <td align="center">/28</td>
    <td align="center"></td>
    <td align="center">ISP-BR-RTR</td>
  </tr>
  <tr>
    <td align="center" rowspan="3">HQ-RTR</td>
    <td align="center">ens256</td>
    <td align="center">172.16.4.2</td>
    <td align="center">/28</td>
    <td align="center">172.16.4.1</td>
    <td align="center">ISP-HQ-RTR</td>
  </tr>
  <tr>
    <td align="center">vlan200/ens224</td>
    <td align="center">192.168.200.1</td>
    <td align="center">/28</td>
    <td align="center"></td>
    <td align="center">HQ-RTR-CLI</td>
  </tr>
  <tr>
    <td align="center">vlan100/ens161</td>
    <td align="center">192.168.100.1</td>
    <td align="center">/26</td>
    <td align="center"></td>
    <td align="center">HQ-RTR-SRV</td>
  </tr>
  <tr>
    <td align="center" rowspan="2">BR-RTR</td>
    <td align="center">ens224</td>
    <td align="center">172.16.5.2</td>
    <td align="center">/28</td>
    <td align="center">172.16.5.1</td>
    <td align="center">ISP-BR-RTR</td>
  </tr>
  <tr>
    <td align="center">ens256</td>
    <td align="center">192.168.0.1</td>
    <td align="center">/27</td>
    <td align="center"></td>
    <td align="center">BR-RTR-SRV</td>
  </tr>
  <tr>
    <td align="center">HQ-SRV</td>
    <td align="center">ens224</td>
    <td align="center">192.168.100.62</td>
    <td align="center">/26</td>
    <td align="center">192.168.100.1</td>
    <td align="center">HQ-RTR-SRV</td>
  </tr>
  <tr>
    <td align="center">BR-SRV</td>
    <td align="center">ens224</td>
    <td align="center">192.168.0.2</td>
    <td align="center">/27</td>
    <td align="center">192.168.0.1</td>
    <td align="center">BR-RTR-SRV</td>
  </tr>
  <tr>
    <td align="center">HQ-CLI</td>
    <td align="center">ens224</td>
    <td align="center">192.168.200.##(DHCP)</td>
    <td align="center">/28</td>
    <td align="center">192.168.200.1</td>
    <td align="center">HQ-RTR-CLI</td>
  </tr>
</table>
<p align="center"><strong>Таблица адресации</strong></p>

</br>

## <p align="center">> Настройка адрессации <</p>

</br>

### ISP
- ### **`ISP`** - Настройка в сторону **`провайдера`**


1. **Имя профиля:** задаётся произвольно, для удобства назовём **`INTERNET`**

2. **Устройство:** ставим интерфейс смотрящий **В ПРОВАЙДЕР**, то есть **`ens192`**  - (В **ПРИМЕРЕ** неправильно!!!)

3. **Конфигурация IPv4:** Оставляем **`Автоматически`** - она же **DHCP/Auto**"

<p align="center">
  <img src="https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/адресация1.png" alt="" />
</p>

</br>

#
### ISP
- ### Настройка интерфейса `ISP` в сторону `HQ-RTR`

1. **Имя профиля** задаём понятное имя соединения **`ISP_HQ-RTR`**

2. **Устройство:** ставим интерфейс смотрящий **В ОФИС HQ**, то есть **`ens161`**  - (В **ПРИМЕРЕ** неправильно!!!)

3. **Конфигурация IPv4:** Ставим **`ВРУЧНУЮ`** - она же **Manual**"

     + **Адреса:** `172.16.4.1/28`

<p align="center">
  <img src="https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/примерАдресацияВhq.png" alt="" />
</p>

</br>

#
### ISP
- ### Настройка интерфейса `ISP` в сторону `BR-RTR` 


1. **Имя профиля** задаём понятное имя соединения **`ISP_BR-RTR`**

2. **Устройство:** ставим интерфейс смотрящий **В ОФИС BR**, то есть **`ens224`**  - (В **ПРИМЕРЕ** неправильно!!!)

3. **Конфигурация IPv4:** Ставим **`ВРУЧНУЮ`** - она же **Manual**"

     + **Адреса:** `172.16.5.1/28`

<p align="center">
  <img src="https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/ISP_BR.png" alt="" />
</p>

</br>

#
### HQ-RTR
- ### Настройка адресации на `HQ-RTR` в сторону `ISP`


1. **Имя профиля** задаём понятное имя соединения **`HQ-RTR_ISP`**

2. **Устройство:** ставим интерфейс смотрящий **В ISP**, то есть **`ens256`**  - (В **ПРИМЕРЕ** неправильно!!!)

3. **Конфигурация IPv4:** Ставим **`ВРУЧНУЮ`** - она же **Manual**"

     + **Адреса:** `172.16.4.2/28`
     + **Шлюз:** `172.16.4.1`
     + **Серверы DNS:** `77.88.8.8`
<p align="center">
  <img src="https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/HQ_isp.png" alt="" />
</p>

</br>



#
### BR-RTR
- ### Настройка адресации на `BR-RTR` в сторону `ISP`



1. **Имя профиля** задаём понятное имя соединения **`BR-RTR_ISP`**

2. **Устройство:** ставим интерфейс смотрящий **В ISP**, то есть **`ens224`**  - (В **ПРИМЕРЕ** неправильно!!!)

3. **Конфигурация IPv4:** Ставим **`ВРУЧНУЮ`** - она же **Manual**"

     + **Адреса:** `172.16.5.2/28`
     + **Шлюз:** `172.16.5.1`
     + **Серверы DNS:** `77.88.8.8`
<p align="center">
  <img src="https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/BR_ISP.png" alt="" />
</p>

</br>

</details>

<br/>

## Настройка адресации в текстовом файле  
(Если настроил в *nmtui*, сюда не лезь)

<br/>
<details>
<summary><strong>Настройка в файле</strong></summary>
<br/>

# Настройка адресации всех устройств производится в файле `/etc/network/interfaces`
Конфигурация файла на примере *ISP*:
```
nano /etc/network/interfaces
```

```
# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
allow-hotplug ens192
iface ens192 inet dhcp

auto ens161
iface ens161 inet static
address 172.16.4.1
netmask 255.255.255.240

auto ens224 
face ens224 inet static
address 172.16.5.1
netmask 255.255.255.240
```
По этому примеру настраиваются все интерфейсы на всех устройствах. Заменяются только *адреса*, *маски*, *интерфейсы*

</details>
<br/>

## ✔️ Задание 2

### Настройка ISP

- Настройте адресацию на интерфейсах:

  - Интерфейс, подключенный к магистральному провайдеру, получает адрес по DHCP **[Выполнено в задании 1]**

  - Настройте маршруты по умолчанию там, где это необходимо 

  - Интерфейс, к которому подключен HQ-RTR, подключен к сети 172.16.4.0/28 **[Выполнено в задании 1]**

  - Интерфейс, к которому подключен BR-RTR, подключен к сети 172.16.5.0/28 **[Выполнено в задании 1]**

  - На ISP настройте динамическую сетевую трансляцию в сторону HQ-RTR и BR-RTR для доступа к сети Интернет

<br/>

<details>
<summary><strong>[Решение]</strong></summary>
<br/>


### Настройка динамической сетевой трансляции на `ISP`

```
echo net.ipv4.ip_forward=1 > /etc/sysctl.conf
apt-get install iptables iptables-persistent –y  
iptables –t nat –A POSTROUTING –s 172.16.4.0/28 –o ens192 –j MASQUERADE  
iptables –t nat –A POSTROUTING –s 172.16.5.0/28 –o ens192 –j MASQUERADE
netfilter-persistent save
systemctl restart netfilter-persistent  
```

> Для проверки можно использовать команду: **`iptables –L –t nat`** - должны высветится в Chain POSTROUTING две настроенные подсети

#

</details>

<br/>

## ✔️ Задание 3

> [!WARNING]
> Обратите внимание, что группа **wheel** не используется!
>
>  Если возникли проблемы с привилегиями(что странно-_-), то обратите внимание на раздел **[Решение проблем](https://github.com/Flicks1383/Demo2025_debian/tree/main?tab=readme-ov-file#решения-проблем)**

### Создание локальных учетных записей

- Создайте пользователя sshuser на серверах HQ-SRV и BR-SRV

  - Пароль пользователя sshuser с паролем P@ssw0rd

  - Идентификатор пользователя 1010

  - Пользователь sshuser должен иметь возможность запускать sudo без дополнительной аутентификации.

- Создайте пользователя net_admin на маршрутизаторах HQ-RTR и BR-RTR

  - Пароль пользователя net_admin с паролем P@$$word

  - При настройке на EcoRouter пользователь net_admin должен обладать максимальными привилегиями

  - При настройке ОС на базе Linux, запускать sudo без дополнительной аутентификации

<br/>

<details>
<summary><strong>[Решение]</strong></summary>
<br/>

### Создание учёток `sshuser` производится на HQ-SRV и BR-SRV

**1.** Создаём sshuser следующими командами:
```
useradd sshuser -u 1010
passwd sshuser
P@ssw0rd
```
<br/>

**2.** После чего даем пользователю *root* права:
```yml
usermod -aG sudo sshuser
```

<br/>

**3.** Добавляем следующую строку в **`/etc/sudoers`**:
```yml
sshuser ALL=(ALL) NOPASSWD:ALL
```
> Позволяет запускать **sudo** без аутентификации

<br/>

**4.** Создаем и задаем необходимые права на домашнюю папку
```
mkdir /home/sshuser
chown sshuser:sshuser /home/sshsuer
chmod 700 /home/sshuser
```
<br/>

### Пользователь `net_admin` на *HQ-RTR и BR-RTR*

**1.** Создаём **`net_admin`**, следующими командами, но уже без `-u 1010` и с новым паролем:
```
useradd net_admin
passwd net_admin
P@$$word
```
<br/>

**2.** После чего даем пользователю *root* права:
```yml
usermod -aG sudo sshuser
```
<br/>

**3.** Добавляем следующую строку в **`/etc/sudoers`**:
```yml
net_admin ALL=(ALL) NOPASSWD:ALL
```
> Позволяет запускать **sudo** без аутентификации
<br/>

**4.** Создаем и задаем необходимые права на домашнюю папку
```
mkdir /home/net_admin
chown sshuser:sshuser /home/net_admin
chmod 700 /home/net_admin
```
<br/>

</details>

<br/>

## ✔️ Задание 4

### Настройте на интерфейсе HQ-RTR в сторону офиса HQ виртуальный коммутатор

- Сервер HQ-SRV должен находиться в ID VLAN 100
- Клиент HQ-CLI в ID VLAN 200
- Создайте подсеть управления с ID VLAN 999
- Основные сведения о настройке коммутатора и выбора реализации разделения на VLAN занесите в отчёт

<br/>

<details>
<summary>Настройка в nmtui</summary>
<br/>

## Конфигурация VLAN на HQ-RTR
### Первым делом необхходимо создать VLAN в утилите `nmtui`:

<p align="center">
  <img src="https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/VLAN%D0%A1%D0%BE%D0%B7%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5.png" alt="" />
</p>

После чего требуется настроить его название и интерфейс (название интерфейса берете с настроек *nmtui*) а также его MAC адрес:

<p align="center">
  <img src="https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/VLAN%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0.png" alt="" />
</p>

После чего аналогичным образом создаем интерфейс для клиентского компа и настраиваем его.  
Далее переходим на сервер, создаем и переходим к конфигурации *VLAN'а*

<p align="center">
  <img src="https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/VLAN%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0SRV.png" alt="" />
</p>

После чего переходим на клиентский комп и при помощи *nmtui* создаем VLAN и настраиваеи его.  

<p align="center">
  <img src="https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/VLANcli.png" alt="" />
</p>

</details>

<br/>
 
<br/>

<details>
<summary>Настройка в файле</summary>
<br/>

## Для начала установи пакет *vlan*:
```
apt install vlan
```

После чего добавляем модуль *`modprobe 8021q`* командой:
```
echo 8021q >> /etc/modules
```
Далее переходим к конфигурации файла `/etc/network/interfaces` и приводим ее к виду:

```
# The primary network interface
allow-hotplug ens161
iface ens161 inet static
address 172.16.4.2
netmask 255.255.255.240
gateway 172.16.4.1

auto ens192
iface ens192 inet dhcp

auto ens192.100
iface ens192.100 inet static
address 192.168.100.1
netmask 255.255.255.192
dns-nameservers 192.168.100.62

auto ens192.200
iface ens192.200 inet static
address 192.168.200.1/28
dns-nameservers 192.168.100.62
```

</details>

<br/>

## ✔️ Задание 5

### Настройка безопасного удаленного доступа на серверах HQ-SRV и BR-SRV

- Для подключения используйте порт 2024
- Разрешите подключения только пользователю sshuser
- Ограничьте количество попыток входа до двух
- Настройте баннер «Authorized access only»

<br/>

<details>
<summary><strong>[Решение]</strong></summary>
<br/>

### SSH настраиваем на HQ-SRV и BR-SRV

**1.** Для настройки **SSH** необходимо его установить коммандой:
```
apt-get install openssh-server
```

</br>

**2.** После чего необходимо добавить строчки в файл **`/etc/ssh/sshd_config`**:
```
Port 2024
MaxAuthTries 2
PasswordAuthentication yes
Banner /etc/ssh/bannermotd
AllowUsers  sshuser
           ^ - это TAB
```
<br/>

**3.** После чего требуется создать файл **`/etc/ssh/bannermotd`** и привести его в следующую форму:
```
----------------------
Authorized access only
----------------------
```
</br>

**4.** Далее необходимо перезапустить **`SSH`** коммандой:
  
```
systemctl restart sshd
```


</details>

<br/>

## ✔️ Задание 6

### Между офисами HQ и BR необходимо сконфигурировать IP-туннель

> [!WARNING]
> **Не используйте** устройства с именем **`tun0`, `gre0` и `sit0`**, т.к они являются зарезервированными в iproute2 («base devices») и имеют особое поведение.

> [!NOTE]
> Для настройки **`GRE - туннеля`**, как и **`адресации`**, удобнее из-за интерфейса будет выбрать **`nmtui`**

- Сведения о туннеле занесите в отчёт  
- На выбор технологии GRE или IP in IP

<br/>

<details>
<summary><strong>Настройка в nmtui</strong></summary>
<br/>

# > Конфигурация GRE туннеля <

</br>

- ### Настройка производится на **HQ-RTR** и **BR-RTR**
> Адреса назначаются из одной подсети - **`это важно!`**

### HQ-RTR

**1.** Заходим в **`nmtui`**

**2.** Выбираем справа параметр: **`"add"`** он же **`Добавить`**

**3.** Выбираем пункт: **`IP-tunnel`**

**4.** Задаём понятное `имя соединения`: **`«tun1»`**

**5.** Задаём `устройство`: **`tun1`**

**6.** `Режим работы/mode`: **`GRE`**

**7.** В `родительский/parent` указываем интерфейс в сторону ISP: **`[ens256]`**

**8.** Задаём «Локальный IP»: **`(IP на интерфейсе HQ-RTR в сторону IPS 172.16.4.2)`**

**9.** задаём «Удалённый\Remote IP»: **`(IP на интерфейсе BR-RTR в сторону ISP 172.16.5.2)`**
    
  **Конфигурация IPv4: `Manual/Вручную`**
   
  **10.** задаём `адрес IPv4` для туннеля **`172.16.0.1/30`**
  
  ❗ ❗ ❗ **Для корректной работы протокола динамической маршрутизации требуется увеличить параметр `TTL` на интерфейсе туннеля**:

      nmcli connection modify tun1 ip-tunnel.ttl 64
      
      systemctl restart networking **или** NetworkManager
      
<p align="center">
  <img src="https://github.com/Flicks1383/Demo2025_debian/blob/main/Module1/iptunnel.gif" alt="" />
</p>

#

### BR-RTR

**1.** Заходим в **`nmtui`**

**2.** Выбираем справа параметр: **`"add"`** он же **`Добавить`**

**3.** Выбираем пункт: **`IP-tunnel`**

**4.** Задаём понятное `имя соединения`: **`«tun1»`**

**5.** Задаём `устройство`: **`tun1`**

**6.** `Режим работы/mode`: **`GRE`**

**7.** В `родительский/parent` указываем интерфейс в сторону ISP: **`[ens224]`**

**8.** Задаём «Локальный IP»: **`(IP на интерфейсе HQ-RTR в сторону IPS 172.16.5.2)`**

**9.** задаём «Удалённый\Remote IP»: **`(IP на интерфейсе BR-RTR в сторону ISP 172.16.4.2)`**
    
  **Конфигурация IPv4: `Manual/Вручную`**
   
  **10.** задаём `адрес IPv4` для туннеля **`172.16.0.2/30`**
  
  ❗ ❗ ❗ **Для корректной работы протокола динамической маршрутизации требуется увеличить параметр `TTL` на интерфейсе туннеля**:

      nmcli connection modify tun1 ip-tunnel.ttl 64
      
      systemctl restart networking **или** NetworkManager
      
</details>

<br/>

<br/>

<details>
<summary><strong>Настройка в файле</strong></summary>
<br/>

Для настройки *GRE* туннеля на *HQ-RTR* переходим в файл конфигурации
```
nano /etc/network/interfaces
```

И приводим добавляем следующий текст:
```
auto gre1
iface gre1 inet tunnel
address 172.16.0.1
netmask 255.255.255.252
mode gre
local 172.16.4.2
endpoint 172.16.5.2
ttl 64
```

Для настройки *GRE* туннеля на *BR-RTR* переходим в файл конфигурации
```
nano /etc/network/interfaces
```

И приводим добавляем следующий текст:
```
auto gre1
iface gre1 inet tunnel
address 172.16.0.2
netmask 255.255.255.252
mode gre
local 172.16.5.2
endpoint 172.16.4.2
ttl 64
```
</details>

<br/>

## ✔️ Задание 7

### Обеспечьте динамическую маршрутизацию: ресурсы одного офиса должны быть доступны из другого офиса. Для обеспечения динамической маршрутизации используйте `link state` протокол на ваше усмотрение

- Разрешите выбранный протокол только на интерфейсах в ip туннеле  
- Маршрутизаторы должны делиться маршрутами только друг с другом  
- Обеспечьте защиту выбранного протокола посредством парольной защиты  
- Сведения о настройке и защите протокола занесите в отчёт

<br/>

<details>
<summary><strong>[Решение]</strong></summary>
<br/>


## Настройка `OSPF` производится HQ-RTR и BR-RTR:

</br>

### HQ-RTR

**1.** Устанавливаем пакет `FRR`

```
sudo apt install -y frr
```

**2.** В конфигурационном файле `/etc/frr/daemons` необходимо активировать выбранный протокол `OSPF` для дальнейшей реализации его настройки:

```
nano /etc/frr/daemons

!!! Ищем следующую строку и меняем с (no) на (yes)
ospfd = yes

```

**3.** Далее перезаргужаем и добавляем в автозагрузку службу **`FRR`**

```
systemctl restart frr
systemctl enable --now frr
```

**4.** Переходим в интерфейс управления симуляцией **`FRR`** командой:
```
vtysh
```

**5.** Пишем команды для настройки **маршрутизации:**
 
```
conf t
router ospf
  passive-interface default
  router-id 1.1.1.1
  network 172.16.0.0/30 area 0
  network 192.168.100.0/26 area 1
  network 192.168.200.0/28 area 2
  area 0 authentication
exit

int tun1
  no ip ospf network broadcast
  no ip ospf passive
  ip ospf authentication
  ip ospf authentication-key password
(config-if)exit
(config)exit
#write
```
<br>

### BR-RTR

**1-4.** Пункты такие же как и в HQ-RTR

**5.** Пишем команды для настройки **маршрутизации:**

**Меняется:** 

- `id-router: 2.2.2.2`

- `network 192.168.0.0/27 area 3`

- `network 172.16.0.0/30 area 0`

```
conf t
router ospf 1
  passive-interface default
  router-id 2.2.2.2
  network 192.168.0.0/27 area 0
  network 172.16.0.0/30 area 0
  area 0 authentication
exit

int tun1
  no ip ospf network broadcast
  no ip ospf passive
  ip ospf authentication
  ip ospf authentication-key password
(config-if)exit
(config)exit
#write
```

### ПРОВЕРКА

Пингуем: **`BR-SRV - > HQ-SRV`** и **`BR-SRV - > HQ-CLI`**

Проверка в **FRR**:

```
vtysh
  show ip ospf neighbor
  show ip route ospf
```

</details>

<br/>

## ✔️ Задание 8

### Настройка динамической трансляции адресов

> [!NOTE]
> Пул адрессов можно посмотреть в таблице <strong>[Задания 1](https://github.com/Flicks1383/Demo2025_debian/tree/main/Module1#задание-1)</strong> или же отталкиваться от вашей адрессации.

- Настройте динамическую трансляцию адресов для обоих офисов.  
- Все устройства в офисах должны иметь доступ к сети Интернет

<br/>

<details>
<summary><strong>[Решение]</strong></summary>
<br/>

# > Настройка динамичесткой трансляции адресов <

<br/>

> ### Настройка на `ISP выполнена` в [Задании 2](https://github.com/Flicks1383/Demo09.02.06_2025/tree/main/module1#задание-2)

### Настройка динамической сетевой трансляции на `HQ-RTR`
```
apt-get install iptables iptables-persistent –y
iptables –t nat –A POSTROUTING –s 192.168.100.0/26 –o ens256 –j MASQUERADE
iptables –t nat –A POSTROUTING –s 192.168.200.0/28 –o ens256 –j MASQUERADE
netfilter-persistent save
systemctl restart netfilter-persistent  
```
### Настройка динамической сетевой трансляции на `BR-RTR`

```
apt-get install iptables iptables-persistent –y
iptables –t nat –A POSTROUTING –s 192.168.0.0/27 –o ens224 –j MASQUERADE
netfilter-persistent save
systemctl restart netfilter-persistent  
```

</br>

</details>

</br>

## ✔️ Задание 9

### Настройка протокола динамической конфигурации хостов

- Настройте нужную подсеть  
- Для офиса HQ в качестве сервера DHCP выступает маршрутизатор HQ-RTR.  
- Клиентом является машина HQ-CLI.  
- Исключите из выдачи адрес маршрутизатора  
- Адрес шлюза по умолчанию – адрес маршрутизатора HQ-RTR.  
- Адрес DNS-сервера для машины HQ-CLI – адрес сервера HQ-SRV.  
- DNS-суффикс для офисов HQ – au-team.irpo  
- Сведения о настройке протокола занесите в отчёт

<br/>

<details>
<summary><strong>[Решение]</strong></summary>
<br/>

# > Настройка динамической конфигурации хостов <

<br/>

**1.** Устанавливаем сам **DHCP-сервер**:  
```
apt install isc-dhcp-server
```
<br/>

**2.** После чего переходим в конфигурацию файла `/etc/dhcp/dhcpd.conf` и добавляем следующие строчки:
```
subnet 192.168.200.0 netmask 255.255.255.240 {
  range 192.168.200.2 192.168.200.14;
  option domain-name-servers 192.168.100.62;
  option domain-name "au-team.irpo";
  option routers 192.168.200.1;
  default-lease-time 600;
  max-lease-time 7200;
}
```
<br/>

**3.** После чего переходим в конфигурацию файла `/etc/default/isc-dhcp-server` и меняем ее добалвяя данный текст:
```
INTERFACESv4="ens161.200" - порт смотрящий в сторону CLI
```

<br/>

**4.** Включаем сервиc **`DHCP`** и добавим в автозагрузку на **`HQ-RTR`**:
```
systemctl start isc-dhcp-server
systemctl enable isc-dhcp-server
```

**5.** Далее на клиенсткой машине необходимо в настройках адаптера выбрать **DHCP** и проверить работоспособность
<br/>

</details>

</br>

## ✔️ Задание 10

### Настройка DNS для офисов HQ и BR  
- Основной DNS-сервер реализован на HQ-SRV.  
- Сервер должен обеспечивать разрешение имён в сетевые адреса устройств и обратно в соответствии с таблицей 2  
- В качестве DNS сервера пересылки используйте любой общедоступный DNS сервер  

<br/>

<table align="center">
  <tr>
    <td align="center">Устройство</td>
    <td align="center">Запись</td>
    <td align="center">Тип</td>
  </tr>
  <tr>
    <td align="center">HQ-RTR</td>
    <td align="center">hq-rtr.au-team.irpo</td>
    <td align="center">A,PTR</td>
  </tr>
  <tr>
    <td align="center">BR-RTR</td>
    <td align="center">br-rtr.au-team.irpo</td>
    <td align="center">A</td>
  </tr>
  <tr>
    <td align="center">HQ-SRV</td>
    <td align="center">hq-srv.au-team.irpo</td>
    <td align="center">A,PTR</td>
  </tr>
  <tr>
    <td align="center">HQ-CLI</td>
    <td align="center">hq-cli.au-team.irpo</td>
    <td align="center">A,PTR</td>
  </tr>
  <tr>
    <td align="center">BR-SRV</td>
    <td align="center">br-srv.au-team.irpo</td>
    <td align="center">A</td>
  </tr>
  <tr>
    <td align="center">HQ-RTR</td>
    <td align="center">moodle.au-team.irpo</td>
    <td align="center">CNAME</td>
  </tr>
  <tr>
    <td align="center">BR-RTR</td>
    <td align="center">wiki.au-team.irpo</td>
    <td align="center">CNAME</td>
  </tr>
</table>

<p align="center"><strong>Таблица 2</strong></p>

<br/>

<details>
<summary><strong>[Решение]</strong></summary>

  <br/>

# > Настройка DNS <
### Основной DNS-сервер реализован на `HQ-SRV`
<br/>

### HQ-SRV

 <br/>

**1.** Для работы с **DNS** требуется установить **`bind`** и доп. пакет командой:

```
apt-get install bind9 bind9-utils
```
<br/>

**2.** Далее необходимо сконфигурировать файл **`/etc/bind/named.conf.option`** таким образом:

```
listen-on { 127.0.0.1; 192.168.100.0/26; 192.168.200.0/28; 192.168.0.0/27; };
forwarders { 77.88.8.8; };
recursion yes;
allow-query { 127.0.0.1; 192.168.100.0/26; 192.168.200.0/28; 192.168.0.0/27; };
allow-query-cache { 127.0.0.1; 192.168.100.0/26; 192.168.200.0/28; 192.168.0.0/27; };
allow-recursion { 127.0.0.1; 192.168.100.0/26; 192.168.200.0/28; 192.168.0.0/27; };
dnssec-validation no;
```
  <br/>

**3.** Конфигурация ключей **`rndc`**:
```
rndc-confgen > /etc/rndc.key 
```
</br>

**❗ Для проверки, можно использовать комманду:** 

```
named-checkconf
```
</br>

**4.** Далее необходимо запустить **утилиту** коммандой:
```
systemctl enable --now named
```
</br>

**5.** Далее требуется изменить конфигурацию файла **`/etc/resolv.conf`**:

```
search au-team.irpo
nameserver 127.0.0.1
nameserver 192.168.100.62
nameserver 77.88.8.8
search yandex.ru
```
</br>

**6.** После чего требуется прописать в **`/etc/bind/named.conf.local`**:

```
zone "au-team.irpo" {
  type master;
  file "/etc/bind/au-team.irpo.db";
};

zone "100.168.192.in-addr.arpa" {
  type master;
  file "/etc/bind/100.168.192.in-addr.arpa";
};

zone "200.168.192.in-addr.arpa" {
  type master;
  file "/etc/bind/200.168.192.in-addr.arpa";
};

zone "0.168.192.in-addr.arpa" {
  type master;
  file "/etc/bind/0.168.192.in-addr.arpa";
};
```
</br>

**7.** Далее следующими командами **создаём копию** файла и присваиваем права:

```
cp /etc/bind/db.local /etc/bind/au-team.irpo
```

</br>

**8.** После чего приводим **файл `/etc/bind/au-team.irpo`** к следующему виду:

```
$TTL    1D
@       IN      SOA     au-team.irpo. root.au-team.irpo. (
                                2024102200      ; serial
                                12H             ; refresh
                                1H              ; retry
                                1W              ; expire
                                1H              ; ncache
                        )
        IN      NS      au-team.irpo.
        IN      A       192.168.100.62
hq-rtr  IN      A       192.168.100.1
br-rtr  IN      A       192.168.0.1
hq-srv  IN      A       192.168.100.62
hq-cli  IN      A       192.168.200.2
br-srv  IN      A       192.168.0.2
moodle  IN      CNAME   hq-rtr
wiki    IN      CNAME   hq-rtr
```
</br>

</br>

**9.** После чего **создаем файлы** командами:
```
cp /etc/bind/db.127 /etc/bind/100.168.192.in-addr.arpa
cp /etc/bind/db.127 /etc/bind/200.168.192.in-addr.arpa
cp /etc/bind/db.127 /etc/bind/0.168.192.in-addr.arpa
```
</br>

</br>

**10.** После изменений файл **`100.168.192.in-addr.arpa`** выглядит так:
```
$TTL    1D
@       IN      SOA     au-team.irpo. root.au-team.irpo. (
                                2024102200      ; Serial
                                12H             ; Refresh
                                1H              ; Retry
                                1W              ; Expire
                                1H              ; Ncache
                        )
        IN      NS      localhost.
1       IN      PTR     hq-rtr.au-team.irpo.
62      IN      PTR     hq-srv.au-team.irpo.
```

</br>

**11.** После изменений файл **`200.168.192.in-addr.arpa`** выглядит так:
```
$TTL    1D
@       IN      SOA     au-team.irpo. root.au-team.irpo. (
                                2024102200      ; Serial
                                12H             ; Refresh
                                1H              ; Retry
                                1W              ; Expire
                                1H              ; Ncache
                        )
        IN      NS      localhost.
14      IN      PTR     hq-cli.au-team.irpo.
```

</br>

**12.** После изменений файл **`0.168.192.in-addr.arpa`** выглядит так:
```
$TTL    1D
@       IN      SOA     au-team.irpo. root.au-team.irpo. (
                                2024102200      ; Serial
                                12H             ; Refresh
                                1H              ; Retry
                                1W              ; Expire
                                1H              ; Ncache
                        )
        IN      NS      localhost.
1       IN      PTR     br-rtr.au-team.irpo.
2       IN      PTR     br-srv.au-team.irpo.
```

### ❗ ❗ ❗ Все пробелы выше ^ ставятся TAB'ом

</br>

</br>

**13.** После чего можно проверить **ошибки** командой:
```
named-checkconf -z
```
</br>

**14.** А также перезапускаем **`bind`** командой:

```
systemctl restart named bind9
```
</br>

**15.** Проверить работоспособность можно **командой**:
```
nslookup **IP-адрес/DNS-имя**
```
</br>

</details>

<br/>

## ✔️ Задание 11

### Настройте часовой пояс на всех устройствах, согласно месту проведения экзамена

<br/>

<details>
<summary>[Решение]</summary>
<br/>

# > Настройте часовой пояс на всех устройствах <
- На Linux настраивается часовой пояс командой
```
timedatectl set-timezone Asia/Tomsk
```  
- На EcoRouter настраивается часовой пояс командой
```
ntp timezone utc+7
```

</details>
