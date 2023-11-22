# DEMO2023
Step-by-step guide for DEMO 2023-2024| WIP

## [Задание](https://bom.firpo.ru/file/1158/%D0%9A%D0%9E%D0%94%2009.02.06-1-2024%20%D0%A2%D0%BE%D0%BC%201.pdf)

## Описание задания 
Образец задания для демонстрационного экзамена по комплекту оценочной документации.

### Модуль 1: Выполнение работ по проектированию сетевой инфраструктуры
## **Задание 1 модуля 1** 
**1.	Выполните базовую настройку всех устройств:**
**a.	Присвоить имена в соответствии с топологией:**
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/f1bffd00-dfbb-4812-aae3-00f00e490c49)

### **ISP**
```
su -
toor
nano /etc/hostname
ISP
Нажать: ctrl-x
Нажать: y
```
```
nano /etc/hosts
127.0.1.1		ISP
Нажать: ctrl-x
Нажать: y
reboot
```

### **HQ-R**
```
su -
toor
nano /etc/hostname
HQ-R
Нажать: ctrl-x
Нажать: y
```
```
nano /etc/hosts
127.0.1.1		HQ-R
Нажать: ctrl-x
Нажать: y
reboot
```

### **HQ-SRV**
```
su -
toor
nano /etc/hostname
HQ-SRV
Нажать: ctrl-x
Нажать: y
```
```
nano /etc/hosts
127.0.1.1		HQ-SRV
Нажать: ctrl-x
Нажать: y
reboot
```
### **BR-R**
```
su -
toor
nano /etc/hostname
BR-R
Нажать: ctrl-x
Нажать: y
```
```
nano /etc/hosts
127.0.1.1		BR-R
Нажать: ctrl-x
Нажать: y
reboot
```

### **BR-SRV**

```
su -
toor
nano /etc/hostname
BR-SRV
Нажать: ctrl-x
Нажать: y
```
```
nano /etc/hosts
127.0.1.1		BR-SRV
Нажать: ctrl-x
Нажать: y
reboot
```

b.	Рассчитайте IP-адресацию IPv4 и IPv6. Необходимо заполнить таблицу №1, чтобы эксперты могли проверить ваше рабочее место. 
Таблица №1
| Имя устройств  | IP |
| ------------- | ------------- |
| CLI  | 10.0.1.2/24 255.255.255.0  |
| ISP | 10.0.1.1/24 255.255.255.0  |
| HQ-R  | 192.168.0.1/24 255.255.255.0  |
| HQ-SRV  | 10.0.0.2/26 255.255.255.192  |
| BR-R  | 192.168.1.1/24 255.255.255.0  |
| BR-SRV  | 10.0.2.2/28 255.255.255.240  |
| HQ-CLI  | 10.0.0.3/26 255.255.255.192  |
| HQ-AD  | 10.0.0.4/26 255.255.255.192  |

c.	Пул адресов для сети офиса BRANCH - не более 16 

255.255.255.240 = /28

d.	Пул адресов для сети офиса HQ - не более 64

255.255.255.192 = /26

2.	Настройте внутреннюю динамическую маршрутизацию по средствам FRR. Выберите и обоснуйте выбор протокола динамической маршрутизации из расчёта, что в дальнейшем сеть будет масштабироваться. 
**Настройка FRR**
Настройку динамическое маршрутизации производим с помощью протокола **OSPF** – потому что…

## **ISP**

```
su -
toor
nano /etc/sysctl.conf
```
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/8368bfdf-5c65-497b-b42e-e2737bee8f7c)
```
ctrl-x
y
sysctl -p
```

## **HQ-R**

```
apt install frr
nano /etc/frr/daemon
меняем строчку
ospfd=no на строчку
ospfd=yes
ctrl-x
y
systemctl restart frr
vtysh
conf t
router ospf
network 10.0.0.0./26 area 0
network 192.168.0.0/24 area 0
network 172.16.0.0/24 area 0
passive-interface ens160
write
exit
systemctl restart frr
```

## **BR-R**

```
apt install frr
nano /etc/frr/daemon
меняем строчку
ospfd=no на строчку
ospfd=yes
ctrl-x
y
systemctl restart frr
vtysh
conf t
router ospf
network 10.0..0./26 area 0
network 192.168.1.0/24 area 0
network 172.16.0.0/24 area 0
passive-interface ens160
write
exit
systemctl restart frr
```

a.	Составьте топологию сети L3.

Рисунок топологии L3

3.	Настройте автоматическое распределение IP-адресов на роутере HQ-R.

## HQ-R
```
nano /etc/default/isc-dhcp-server

![image](https://github.com/NyashMan/DEMO2023/assets/1348639/b1e54a9d-f5f3-4435-866f-a75b6a02bb9d)
ctrl-x
y
nano /etc/dhcp/dhcpd.conf
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/efd64fdb-f88b-4043-8254-77314387a3e9)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/deb9054e-a853-4f3e-a524-cbcf4e1ec4b7)
```
a.	Учтите, что у сервера должен быть зарезервирован адрес.
```
nano /etc/dhcp/dhcpd.conf
hardware ethernet указывается MAC-адрес HQ-SRV
ctrl-x
y
systemctl start isc-dhcp-server
systemctl enable isc-dhcp-server
```
При отсутствии ошибок в конфигурации, сервер запустится корректно.

4.	Настройте локальные учётные записи на всех устройствах в соответствии с таблицей 2.
Таблица №2

| Логин | Пароль | Примечание |
| :---         |     :---:      |          ---: |
| Admin   | P@ssw0rd     | CLI HQ-SRV HQ-R    |
| Branch admin     | P@ssw0rd       | BR-SRV BR-R      |
| Network admin     | P@ssw0rd       | HQ-R BR-R BR-SRV      |
## **CLI**
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/2733e0ed-097a-4479-a11e-190c5b81f6cf)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/17e0b12d-5049-405f-9d92-a987f6f07833)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/9f851637-1de1-426b-9c98-25c30ba4deed)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/1230a187-de3f-41e0-9edf-4386ec7c7e68)

## **HQ-SRV**
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/2733e0ed-097a-4479-a11e-190c5b81f6cf)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/17e0b12d-5049-405f-9d92-a987f6f07833)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/9f851637-1de1-426b-9c98-25c30ba4deed)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/1230a187-de3f-41e0-9edf-4386ec7c7e68)

## **HQ-R**

```
useradd Admin
passwd Admin
P@ssw0rd
P@ssw0rd
```

## **BR-SRV**

![image](https://github.com/NyashMan/DEMO2023/assets/1348639/2733e0ed-097a-4479-a11e-190c5b81f6cf)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/17e0b12d-5049-405f-9d92-a987f6f07833)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/9f851637-1de1-426b-9c98-25c30ba4deed)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/1230a187-de3f-41e0-9edf-4386ec7c7e68)

## **BR-R**

```
useradd Brancadmin
passwd Branchadmin
P@ssw0rd
P@ssw0rd
```

5.	Измерьте пропускную способность сети между двумя узлами HQ-R-ISP по средствам утилиты iperf 3. Предоставьте описание пропускной способности канала со скриншотами.

## **ISP**

```
iperf -s
```

## **HQ-R**
```
iperf -c 192.168.0.1 -f m
```
По результату проверки сделать скриншот.

6.	Составьте backup скрипты для сохранения конфигурации сетевых устройств, а именно HQ-R BR-R. Продемонстрируйте их работу.
## **HQ-R**
```
mkdir /etc/backup/
cd /etc/backup/
touch bckp.sh
chmod +x bckp.sh
nano bckp.sh
#!/bin/bash
cp -r /etc/network /etc/backup/
echo "ok"
crontab -e
*/30 * * * * /etc/backup/bckp.sh
ctrl+x
y
```

## **BR-R**
```
mkdir /etc/backup/
cd /etc/backup/
touch bckp.sh
chmod +x bckp.sh
nano bckp.sh
#!/bin/bash
cp -r /etc/network /etc/backup/
echo "ok"
crontab -e
*/30 * * * * /etc/backup/bckp.sh
ctrl+x
y
```

7.	Настройте подключение по SSH для удалённого конфигурирования устройства HQ-SRV по порту 2222. Учтите, что вам необходимо перенаправить трафик на этот порт посредством контролирования трафика.


8.	Настройте контроль доступа до HQ-SRV по SSH со всех устройств, кроме CLI.  

### Модуль 2: Организация сетевого администрирования
## Задание модуля 2


### Модуль 3: Эксплуатация объектов сетевой инфраструктуры
## Задание модуля 3:
7. Между офисами HQ и BRANCH установите защищенный туннель, позволяющий осуществлять связь между регионами с применением внутренних адресов.

## **HQ-R**
```
nmtui
```
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/a91054c7-85aa-4a7f-a815-54088be61151)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/53b24ffe-6ce5-47d3-b59d-6092ca89e4e3)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/8c7d6301-f676-48b1-a227-78fac3079f91)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/084cbc33-5f66-48ff-8b45-9e0bd87c4fa2)

## **BR-R**
```
nmtui
```
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/bc821ba2-c330-488c-94e3-506c51817731)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/5614ce50-207e-42fb-b3d3-af074b5b7a96)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/28766437-39de-4040-a602-3c108021aad4)
