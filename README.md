# DEMO2023
Step-by-step guide for DEMO 2023-2024| WIP

## [Задание](https://bom.firpo.ru/file/1158/%D0%9A%D0%9E%D0%94%2009.02.06-1-2024%20%D0%A2%D0%BE%D0%BC%201.pdf)

## Описание задания 
Образец задания для демонстрационного экзамена по комплекту оценочной документации.

### Модуль 1: Выполнение работ по проектированию сетевой инфраструктуры
## **Задание 1 модуля 1** 
**1.	Выполните базовую настройку всех устройств:**
**a.	Присвоить имена в соответствии с топологией:**
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/7b6fb5ab-5c65-4058-bb84-afc10982dcac)


### **ISP**
```
su -
toor
hostnamectl set-hostname ISP; exec bash
нажать enter
```

### **CLI**
```
su -
toor
hostnamectl set-hostname CLI; exec bash
нажать enter
```

### **HQ-R**
```
su -
toor
hostnamectl set-hostname HQ-R; exec bash
нажать enter
```

### **HQ-SRV**
```
su -
toor
hostnamectl set-hostname ISP; exec bash
нажать enter
```

### **BR-R**
```
su -
toor
hostnamectl set-hostname BR-R; exec bash
нажать enter
```


### **BR-SRV**

```
su -
toor
hostnamectl set-hostname BR-SRV; exec bash
нажать enter
```

**b.	Рассчитайте IP-адресацию IPv4 и IPv6. Необходимо заполнить таблицу №1, чтобы эксперты могли проверить ваше рабочее место.**  
**Таблица №1**  
| Имя устройств  | IP | Примечание |
| ------------- | ------------- | ------------- |
| CLI  | 10.0.1.2/24 255.255.255.0  |ens33 to ISP |
| ISP | 10.0.1.1/24 255.255.255.0  |ens33 - to CLI;  ens160 to HQ-R; ens224 to BR-R|
| HQ-R  | 192.168.0.1/24 255.255.255.0  |ens192 - to ISP;  ens160 - to HQ  |
| HQ-SRV  | 10.0.0.2/26 255.255.255.192  |ens33 to HQ-R  |
| BR-R  | 192.168.1.1/24 255.255.255.0  |ens160 to BR;  ens33 to ISP  |
| BR-SRV  | 10.0.2.2/28 255.255.255.240  |ens33 to BR-R  |
| HQ-CLI  | 10.0.0.3/26 255.255.255.192  |
| HQ-AD  | 10.0.0.4/26 255.255.255.192  |

**c.	Пул адресов для сети офиса BRANCH - не более 16**

255.255.255.240 = /28

**d.	Пул адресов для сети офиса HQ - не более 64**

255.255.255.192 = /26

**2.	Настройте внутреннюю динамическую маршрутизацию по средствам FRR. Выберите и обоснуйте выбор протокола динамической маршрутизации из расчёта, что в дальнейшем сеть будет масштабироваться.**  
**Настройка FRR**  
Настройку динамическое маршрутизации производим с помощью протокола **OSPF** – потому что…  

## **ISP**

```
su -
toor
nano /etc/sysctl.conf
```
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/cb3edc27-d840-4207-a417-18741d71c650)

```
ctrl-x
y
sysctl -p
```

## **HQ-R**

```
apt install frr
nano /etc/frr/daemons
меняем строчку
ospfd=no на строчку
ospfd=yes
ctrl-x
y
systemctl restart frr
vtysh
conf t
router ospf
network 10.0.1.0/26 area 0
network 192.168.0.0/24 area 0
network 172.16.0.0/24 area 0
int ens160
ip ospf passive
do write
exit
systemctl restart frr
```

## **BR-R**

```
apt install frr
nano /etc/frr/daemons
меняем строчку
ospfd=no на строчку
ospfd=yes
ctrl-x
y
systemctl restart frr
vtysh
conf t
router ospf
network 10.0.2.0./26 area 0
network 192.168.1.0/24 area 0
network 172.16.0.0/24 area 0
int ens160
ip ospf passive
do write
exit
systemctl restart frr
```

**a.	Составьте топологию сети L3.**

Рисунок топологии L3

**3.	Настройте автоматическое распределение IP-адресов на роутере HQ-R.**

## HQ-R
```
nano /etc/default/isc-dhcp-server
```
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/7a69036d-24db-440e-95a7-31ecb03b3a90)

```
ctrl-x
y
nano /etc/dhcp/dhcpd.conf
```
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/d30e9ac4-27d3-4abb-9e6e-e3ab41a9ed28)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/d4fecaab-3776-4d4f-bd27-56a66736c241)

**a.	Учтите, что у сервера должен быть зарезервирован адрес.**
```
nano /etc/dhcp/dhcpd.conf
hardware ethernet указывается MAC-адрес HQ-SRV
```
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/aaddc443-9f67-4286-86d6-2e940047e912)
```
ctrl-x
y
systemctl start isc-dhcp-server
systemctl enable isc-dhcp-server
```
При отсутствии ошибок в конфигурации, сервер запустится корректно.

**4.	Настройте локальные учётные записи на всех устройствах в соответствии с таблицей 2.**  
**Таблица №2**  

| Логин | Пароль | Примечание |
| :---         |     :---:      |          ---: |
| Admin   | P@ssw0rd     | CLI HQ-SRV HQ-R    |
| Branch admin     | P@ssw0rd       | BR-SRV BR-R      |
| Network admin     | P@ssw0rd       | HQ-R BR-R BR-SRV      |
## **CLI**
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/c1d0c57c-46ed-4adf-b58d-56584960ba19)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/08241d45-02a3-4b36-91d3-479fd11c5525)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/309c22ab-4edf-4d60-bbf6-2720b0b1c931)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/7cde267c-0f6c-42e1-b8e2-73427c329bb8)

## **HQ-SRV**
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/c1d0c57c-46ed-4adf-b58d-56584960ba19)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/08241d45-02a3-4b36-91d3-479fd11c5525)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/309c22ab-4edf-4d60-bbf6-2720b0b1c931)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/7cde267c-0f6c-42e1-b8e2-73427c329bb8)

## **HQ-R**

```
useradd Admin
passwd Admin
P@ssw0rd
P@ssw0rd
```

## **BR-SRV**

![image](https://github.com/NyashMan/DEMO2023/assets/1348639/c1d0c57c-46ed-4adf-b58d-56584960ba19)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/08241d45-02a3-4b36-91d3-479fd11c5525)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/309c22ab-4edf-4d60-bbf6-2720b0b1c931)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/7cde267c-0f6c-42e1-b8e2-73427c329bb8)

## **BR-R**

```
useradd Brancadmin
passwd Branchadmin
P@ssw0rd
P@ssw0rd
```

**5.	Измерьте пропускную способность сети между двумя узлами HQ-R-ISP по средствам утилиты iperf 3. Предоставьте описание пропускной способности канала со скриншотами.**

## **ISP**

```
iperf -s
```

## **HQ-R**
```
iperf -c 192.168.0.1 -f m
```
По результату проверки сделать скриншот.

**6.	Составьте backup скрипты для сохранения конфигурации сетевых устройств, а именно HQ-R BR-R. Продемонстрируйте их работу.**
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

**7.	Настройте подключение по SSH для удалённого конфигурирования устройства HQ-SRV по порту 2222. Учтите, что вам необходимо перенаправить трафик на этот порт посредством контролирования трафика.**
## **HQ-SRV**
```
nano /etc/ssh/sshd_config
```
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/2fb9a0c0-4f12-44e6-aff3-3866c10ed23f)

```
ctrl-x
y
systemctl restart sshd
```
## **HQ-R**
  
```
systemctl enable --now nftables
nft flush ruleset
nft add table inet nat
nft add chain inet nat prerouting '{ type nat hook prerouting priority 0; }'
nft add rule inet nat prerouting ip daddr 192.168.0.2 tcp dport 22 dnat to 10.0.0.1:2222
nft list ruleset
```
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/4bb7bb1f-e2bd-4465-94b5-239bb86f283f)
```
echo "flush ruleset" >> /etc/nftables/nftables.nft
nft -s list ruleset >> /etc/nftables.conf
nft -f /etc/nftables.conf
systemctl restart nftables
```

**8.	Настройте контроль доступа до HQ-SRV по SSH со всех устройств, кроме CLI.**

### Модуль 2: Организация сетевого администрирования
## **Задание модуля 2**


### Модуль 3: Эксплуатация объектов сетевой инфраструктуры
## **Задание модуля 3:**
**7. Между офисами HQ и BRANCH установите защищенный туннель, позволяющий осуществлять связь между регионами с применением внутренних адресов.**

## **HQ-R**
```
nmtui
```
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/76f37371-6e15-47d2-8221-887b43eb08e7)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/9308b996-5283-480b-8643-aa04437562bb)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/b69b2e51-deab-4930-9ce3-ca1382297a28)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/245f306c-0fe7-4f11-a5dd-dd37d0258005)


## **BR-R**
```
nmtui
```
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/777199e9-a63f-47b0-aef8-5a21f1d231ac)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/67fdfae2-7c7a-4769-9f31-155ca3d50940)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/b6e93e7f-948c-4ada-a8af-b79f822aa2d4)
![image](https://github.com/NyashMan/DEMO2023/assets/1348639/1b13096a-fd7b-4328-b81d-e6924cb998b8)

