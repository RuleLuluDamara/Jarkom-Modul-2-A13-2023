# Jarkom-Modul-2-A13-2023

## Laporan Resmi Pengerjaan Praktikum Jaringan Komputer

| No. | Anggota               | NRP          |
|-----|----------------------------|--------------|
| 1   | Aida Fitrania Prabasati    | 5025211033   |
| 2   | Rule Lulu Damara           | 5025211050   |
 
## Topologi

![image](https://github.com/RuleLuluDamara/Jarkom-Modul-2-A13-2023/assets/105763198/537fbbc5-3468-470c-b607-411b9ece2293)

## Network Configuration

- Router
  ```bash
  auto eth0
  iface eth0 inet dhcp
  
  auto eth1
  iface eth1 inet static
  	address 192.175.1.1
  	netmask 255.255.255.0
  
  auto eth2
  iface eth2 inet static
  	address 192.175.2.1
  	netmask 255.255.255.0
  ```
- YudhitiraDNSMaster
  ```bash
  auto eth0
  iface eth0 inet static
  	address 192.175.2.2
  	netmask 255.255.255.0
  	gateway 192.175.2.1

  ```
- WerkudaraDNSSlave
  ```bash
  auto eth0
  iface eth0 inet static
  	address 192.175.2.3
  	netmask 255.255.255.0
  	gateway 192.175.2.1

  ```
- ArjunaLoadBalancer
  ```bash
  auto eth0
  iface eth0 inet static
  	address 192.175.2.4
  	netmask 255.255.255.0
  	gateway 192.175.2.1
  ```
- NakulaClient
  ```bash
  auto eth0
  iface eth0 inet static
  	address 192.175.1.2
  	netmask 255.255.255.0
  	gateway 192.175.1.1

  ```
- SadewaClient
   ```bash
  auto eth0
  iface eth0 inet static
    	address 192.175.1.3
    	netmask 255.255.255.0
    	gateway 192.175.1.1

  ```
- AbimanyuWebServer
   ```bash
  auto eth0
  iface eth0 inet static
    	address 192.175.1.4
    	netmask 255.255.255.0
    	gateway 192.175.1.1

  ```
- PrabukusumaWebServer
  ```bash
  auto eth0
  iface eth0 inet static
    	address 192.175.1.5
    	netmask 255.255.255.0
    	gateway 192.175.1.1

  ```
- WisanggeniWebServer
   ```bash
  auto eth0
  iface eth0 inet static
    	address 192.175.1.6
    	netmask 255.255.255.0
    	gateway 192.175.1.1

  ```
## Intial Script
Di initial project, file /root/.bashrc pada masing-masing node diubah sehingga saat dijalankan akan langsung melakukan command berikut ini

- Router
  ```bash
  iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.175.0.0/16
  ```
- Master&Slave
  ```bash
  echo nameserver 192.168.122.1 > /etc/resolv.conf
  
  apt-get update
  apt-get install bind9 -y

  ```
- Client
  ```bash
  echo -e '
  nameserver 192.168.122.1
  ' > /etc/resolv.conf
  
  apt-get update
  apt-get install dnsutils
  apt-get install lynx
  ```
- WebServer
  ```bash
    echo -e '
  nameserver 192.168.122.1
  ' > /etc/resolv.conf
  
  apt-get update
  ```

## Question 1

> Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni.

### Script 

Setelah berhasil melakukan konfigurasi dan selesai menjalankan start command, kita akan melakukan testing untuk semua node dengan melakukan ping terhadap google.com.

> Script dibawah ini terdapat pada root semua node, untuk menjalankannya bisa langsung dengan melakukan command bash no1.sh



![image](https://github.com/RuleLuluDamara/Jarkom-Modul-2-A13-2023/assets/105763198/d562c28b-0590-4be7-a99f-7aa3eaeb9d06)

![image](https://github.com/RuleLuluDamara/Jarkom-Modul-2-A13-2023/assets/105763198/f8c461e5-f82b-410a-9133-c1e3995708d0)

