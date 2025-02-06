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
> ip -br a
> 
> ip -c -br a
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
<summary><strong>[Решение]</strong></summary>
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

<br/>

## Настройка динамической сетевой трансляции на ISP

```
echo net.ipv4.ip_forward=1 > /etc/sysctl.conf
apt-get install iptables –y   
iptables –t nat –A POSTROUTING –s 172.16.4.0/28 –o ens192 –j MASQUERADE  
iptables –t nat –A POSTROUTING –s 172.16.5.0/28 –o ens192 –j MASQUERADE  
iptables-save > /etc/sysconfig/iptables  
systemctl restart iptables  
```

> Для проверки можно использовать команду: **`iptables –L –t nat`** - должны высветится в Chain POSTROUTING две настроенные подсети

#

</details>

<br/>

## ✔️ Задание 3

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

- ### Создание учёток на Linux `КРОМЕ ISP`:
```
useradd sshuser -u 1010
passwd P@ssw0rd
```

Добавляем в группу **wheel**:
```yml
usermod -aG wheel sshuser
```

<br/>

Добавляем строку в **`/etc/sudoers`**:
```yml
sshuser ALL=(ALL) NOPASSWD:ALL
```
> Позволяет запускать **sudo** без аутентификации 

Пользователь на *роутере* добавляется и конфигурируется аналогично, но уже с именем *net_admin* и без `-u 1010`
<br/>

</details>

<br/>

## ❌ Задание 4

### Настройте на интерфейсе HQ-RTR в сторону офиса HQ виртуальный коммутатор

- Сервер HQ-SRV должен находиться в ID VLAN 100
- Клиент HQ-CLI в ID VLAN 200
- Создайте подсеть управления с ID VLAN 999
- Основные сведения о настройке коммутатора и выбора реализации разделения на VLAN занесите в отчёт

<br/>

<details>
<summary>[В процессе]</summary>
<br/>

### Конфигурация VLAN на HQ-RTR

----------**В процессе**----------

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

# > Настройка безопасного удаленного доступа на серверах HQ-SRV и BR-SRV <

#

## Для настройки SSH необходимо его установить коммандой 
`apt-get install ssh-server`

#

- После чего необходимо добавить строчки в файл `/etc/openssh/sshd_config`
```
Port 2024
MaxAuthTries 2
PasswordAuthentication yes
Banner /etc/openssh/bannermotd
AllowUsers  sshuser
           ^ - это TAB
```
- После чего требуется создать файл `/etc/openssh/bannermotd`
```
----------------------
Authorized access only
----------------------
```
- Далее необходимо перезапустить SSH коммандой
  
`systemctl restart sshd`

#

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
<summary><strong>[Решение]</strong></summary>
<br/>

# > Конфигурация GRE туннеля <

- Настройка производится на **HQ-RTR** и **BR-RTR**

#



</details>

<br/>
