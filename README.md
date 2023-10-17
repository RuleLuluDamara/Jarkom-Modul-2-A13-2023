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
Di initial project, file ```/root/.bashrc``` pada masing-masing node diubah sehingga saat dijalankan akan langsung melakukan command berikut ini

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

Setelah berhasil melakukan konfigurasi dan selesai menjalankan start command, kita akan melakukan testing untuk semua node dengan melakukan ```ping google.com```.

> Script dibawah ini terdapat pada root semua node, untuk menjalankannya bisa langsung dengan melakukan command bash ```no1.sh```

```bash
echo -e "================="
echo "test akses internet"
ping google.com -c 5
echo -e "================="
```

### Testing 

![image](https://github.com/RuleLuluDamara/Jarkom-Modul-2-A13-2023/assets/105763198/456c1c15-5154-4477-b9a4-e62a35e4f4b9)

![image](https://github.com/RuleLuluDamara/Jarkom-Modul-2-A13-2023/assets/105763198/989de362-e8c1-4db2-862d-5fef0ff709e3)

## Question 2

> Buatlah website utama pada node arjuna dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok.

### Script
Pertama-tama pada node YudhistiraDNSMaster (Master), kita harus konfigurasikan ```/etc/bind/named.conf.local``` dengan domain ```arjuna.a13.com```. Setelah itu buatlah direktori ```/etc/bind/asibkuy```. Kemudian buatlah file ```arjuna.a13.com```. pada direktori yang baru saja dibuat dan isilah file sesuai dengan contoh dibawah ini (setelah command ```mkdir /etc/bind/asibkuy```). Setelah selesai maka kita harus merestart bind9 dengan command service bind9 restart.

> Script dibawah ini terdapat pada root node YudhistiraDNSMaster, untuk menjalankannya bisa langsung dengan melakukan command ```bash no2.sh```

```bash
echo -e '
zone "arjuna.a13.com" {  
        type master;
        file "/etc/bind/asibkuy/arjuna.a13.com";

};
 ' > /etc/bind/named.conf.local

mkdir /etc/bind/asibkuy

echo -e '
$TTL    604800  
@       IN      SOA     arjuna.a13.com. root.arjuna.a13.com. (
                        2023100910	; Serial
                        604800	; Refresh
                        86400		; Retry
                        2419200	; Expire
                        604800 )	; Negative Cache TTL
;
@	IN	NS	arjuna.a13.com.
@       IN	A	192.175.2.4 	; IP 
www	IN      CNAME   arjuna.a13.com.
' >  /etc/bind/asibkuy/arjuna.a13.com

service bind9 restart

```

Kemudian pada node client (NakulaClient & SadewaClient), kita harus lakukan setting nameserver yang ada. Kemudian kita akan mengecek dengan melakukan ```host -t CNAME www.arjuna.a13.com -c 5```.

> Script dibawah ini terdapat pada root node NakulaClient & SadewaClient, untuk menjalankannya bisa langsung dengan melakukan command ```bash no2.sh```

```bash
echo nameserver 192.175.2.2  > /etc/resolv.conf ; IP Yudhistira

echo -e “==================================="
echo "TEST ARAH PING arjuna.a13.com (mengarah ke host IP Arjuna)"
ping arjuna.a13.com
echo -e “==================================="
echo "TEST ARAH PING www.arjuna.a13.com (mengarah ke host IP Arjuna)"
ping www.arjuna.a13.com
echo -e “==================================="
echo "TEST ALIAS (CNAME) (alias dari arjuna.a13.com.)"
host -t CNAME www.arjuna.a13.com.
echo -e “==================================="

```

### Testing

![image](https://github.com/RuleLuluDamara/Jarkom-Modul-2-A13-2023/assets/105763198/9563cf85-8bbc-40b2-a4c4-c7754c129949)


## Question 3

> Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke ```abimanyu.yyy.com``` dan alias ```www.abimanyu.yyy.com```.


### Script
Dengan cara yang sama seperti pada soal 2, Node YudhistiraDNSMaster (Master) harus dikonfigurasikan /etc/bind/named.conf.local dengan domain abimanyu.a13.com. Setelah itu buatlah direktori ```/etc/bind/asibkuy```. Kemudian buatlah file ```abimanyu.a13.com```. pada direktori yang baru saja dibuat dan isilah file sesuai dengan contoh dibawah ini (setelah command ```mkdir /etc/bind/asibkuy```). Setelah selesai maka kita harus merestart bind9 dengan command ```service bind9 restart```.

> Script dibawah ini terdapat pada root node YudhistiraDNSMaster, untuk menjalankannya bisa langsung dengan melakukan command bash ```no3.sh```.

```bash
echo -e '
zone "arjuna.a13.com" {  
        type master;
        file "/etc/bind/asibkuy/arjuna.a13.com";

};
zone "abimanyu.a13.com" {  
        type master;  
        file "/etc/bind/asibkuy/abimanyu.a13.com";
};
 ' > /etc/bind/named.conf.local

mkdir /etc/bind/asibkuy

echo -e '
$TTL    604800  
@       IN      SOA     arjuna.a13.com. root.arjuna.a13.com. (
                        2023100910	; Serial
                        604800	; Refresh
                        86400		; Retry
                        2419200	; Expire
                        604800 )	; Negative Cache TTL
;
@	IN	NS	arjuna.a13.com.
@       IN	A	192.175.2.4 	; IP ArjunaWebServer
www	IN      CNAME   arjuna.a13.com.
' >  /etc/bind/asibkuy/arjuna.a13.com

echo -e '
$TTL    604800  
@       IN      SOA     abimanyu.a13.com. root.abimanyu.a13.com. (
                        2023100910	; Serial
                        604800	; Refresh
                        86400		; Retry
                        2419200	; Expire
                        604800 )	; Negative Cache TTL
;
@	IN	NS	abimanyu.a13.com.
@       IN	A	192.175.1.4 	;  IP AbimanyuWebServer
www	IN      CNAME   abimanyu.a13.com.
parikesit           IN      A       192.175.1.4  ; IP AbimanyuWebServer
' >  /etc/bind/asibkuy/abimanyu.a13.com

service bind9 restart


```

Kemudian pada node client (NakulaClient & SadewaClient), kita harus lakukan setting nameserver yang ada. Kemudian kita akan mengecek dengan melakukan ```host -t CNAME www.abimanyu.a13.com -c 5```.

> Script dibawah ini terdapat pada root node NakulaClient & SadewaClient, untuk menjalankannya bisa langsung dengan melakukan command bash ```no3.sh```.

```bash
echo nameserver 192.175.2.2  > /etc/resolv.conf

echo -e "==================================="
echo "TEST ARAH PING abimanyu.a13.com (mengarah ke host IP Abimanyu)"
ping abimanyu.a13.com
echo -e "==================================="
echo "TEST ARAH PING www.abimanyu.a13.com (mengarah ke host IP Abimanyu)"
ping www.abimanyu.a13.com
echo -e "==================================="
echo "TEST ALIAS (CNAME) (alias dari abimanyu.a13.com.)"
host -t CNAME www.abimanyu.a13.com.
echo -e "==================================="

```

### Testing

![image](https://github.com/RuleLuluDamara/Jarkom-Modul-2-A13-2023/assets/105763198/eb87b0e8-775c-444b-b39b-c804f96ab729)


## Question 4

> Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu

### Script 
Untuk membuat subdomain kita harus menambahkan ```parikesit            IN      A       192.178.2.3             ; IP Abimanyu``` pada file ```/etc/bind/asibkuy/abimanyu.a13.com.``` Kemudian tambahkan juga ```www.parikesit        IN      CNAME   parikesit.abimanyu.a13.com.``` pada file yang sama. Setelah selesai maka kita harus merestart bind9 dengan command ```service bind9 restart```.

Script dibawah ini terdapat pada root node YudhistiraDNSMaster, untuk menjalankannya bisa langsung dengan melakukan command ```bash no4.sh```

```bash
echo -e '
zone "arjuna.a13.com" {  
        type master;
        file "/etc/bind/asibkuy/arjuna.a13.com";

};
zone "abimanyu.a13.com" {  
        type master;  
        file "/etc/bind/asibkuy/abimanyu.a13.com";
};
 ' > /etc/bind/named.conf.local

mkdir /etc/bind/asibkuy

echo -e '
$TTL    604800  
@       IN      SOA     abimanyu.a13.com. root.abimanyu.a13.com. (
                        2023100910	; Serial
                        604800	; Refresh
                        86400		; Retry
                        2419200	; Expire
                        604800 )	; Negative Cache TTL
;
@	IN	NS	abimanyu.a13.com.
@       IN	A	192.175.2.2 	;  IP Yudhistira
www	IN      CNAME   abimanyu.a13.com.
parikesit           IN      A       192.175.1.4  ; IP AbimanyuWebServer
www.parikesit  IN      CNAME   parikesit.abimanyu.a07.com.

' >  /etc/bind/asibkuy/abimanyu.a13.com

service bind9 restart
```

Kemudian pada node client (NakulaClient & SadewaClient), kita harus lakukan setting nameserver yang ada. Kemudian kita akan mengecek dengan melakukan ```host -t CNAME www.abimanyu.a13.com -c 5```.

> Script dibawah ini terdapat pada root node NakulaClient & SadewaClient, untuk menjalankannya bisa langsung dengan melakukan command bash ```no4.sh```.

```bash
echo nameserver 192.175.2.2  > /etc/resolv.conf

echo -e "================================================="
echo "TEST PING SUBDOMAIN (mengarah ke host IP Abimanyu)"
ping parikesit.abimanyu.a13.com.
echo -e "================================================="
```

### Testing

![image](https://github.com/RuleLuluDamara/Jarkom-Modul-2-A13-2023/assets/105763198/8b1d2275-06fd-4d58-a30a-843f40f4143b)

## Question 5

> Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse)

### Script
Untuk membuat reverse domain kita memerlukan tambahan Zone Baru, tambahkan zone tersebut pada file ```/etc/bind/named.conf.local``` sesuai dengan ip kita. Kemudian buatlah sebuah file pada direktori ```/etc/bind/asibkuy/``` dengan nama file "3 byte reverse ip kita".in-addr.arpa. Setelah selesai maka kita harus merestart bind9 dengan command ```service bind9 restart```.

Script dibawah ini terdapat pada root node YudhistiraDNSMaster, untuk menjalankannya bisa langsung dengan melakukan command ```bash no5.sh```

```bash
echo -e '
zone "arjuna.a13.com" {  
        type master;
        file "/etc/bind/asibkuy/arjuna.a13.com";
};
zone "abimanyu.a13.com" {  
        type master;  
        file "/etc/bind/asibkuy/abimanyu.a13.com";
};
zone "1.175.192.in-addr.arpa" {
        type master;
        file "/etc/bind/asibkuy/1.175.192.in-addr.arpa";
};
 ' > /etc/bind/named.conf.local

mkdir /etc/bind/asibkuy

echo -e '
$TTL    604800  
@       IN      SOA     abimanyu.a13.com. root.abimanyu.a13.com. (
                        2023100910	; Serial
                        604800	; Refresh
                        86400		; Retry
                        2419200	; Expire
                        604800 )	; Negative Cache TTL
;
1.175.192.in-addr.arpa.   IN      NS       abimanyu.a13.com.
4                       IN      PTR      abimanyu.a13.com.
' >  /etc/bind/asibkuy/2.175.192.in-addr.arpa

service bind9 restart

```

Kemudian kita akan mengecek reverse domain dengan melakukan ```host -t PTR 192.175.1.4```.

Script dibawah ini terdapat pada root node client (NakulaClient & SadewaClient), untuk menjalankannya bisa langsung dengan melakukan command ```bash no5.sh```

```bash
echo nameserver 192.175.2.2  > /etc/resolv.conf

echo -e "==================================="
echo "TES REVERSE DOMAIN (point to abimanyu.a13.com.)"
host -t PTR 192.175.1.4
echo -e "==================================="
```

### Testing

![image](https://github.com/RuleLuluDamara/Jarkom-Modul-2-A13-2023/assets/105763198/10f7138f-9342-486a-86be-542546ee17cd)

## Question 6
> Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.

### Script

> Script dibawah ini terdapat pada root node YudhistiraDNSMaster, untuk menjalankannya bisa langsung dengan melakukan command bash ```no6.sh```

```bash
echo -e '
zone "arjuna.a13.com" {  
        type master;
        notify yes;
        also-notify {192.175.2.3;};  
        allow-transfer {192.175.2.3;};
        file "/etc/bind/asibkuy/arjuna.a13.com";

};
zone "abimanyu.a13.com" {  
        type master;  
        notify yes;
        also-notify {192.175.2.3;};  
        allow-transfer {192.175.2.3;};
        file "/etc/bind/asibkuy/abimanyu.a13.com";
};
zone "2.175.192.in-addr.arpa" {
        type master;
        file "/etc/bind/asibkuy/2.175.192.in-addr.arpa";
};
zone "1.175.192.in-addr.arpa" {
        type master;
        file "/etc/bind/asibkuy/1.172.192.in-addr.arpa";
};
 ' > /etc/bind/named.conf.local

service bind9 restart

```

> Script dibawah ini terdapat pada root node WerkudaraDNSMaster, untuk menjalankannya bisa langsung dengan melakukan command bash ```no6.sh```

```bash
echo -e '
zone "arjuna.a13.com" {  
        type slave;
        masters {192.175.2.2;};  
        file "/var/lib/bind/arjuna.a13.com";

};
zone "abimanyu.a13.com" {  
        type slave;
        masters {192.175.2.2;};  
        file "/var/lib/bind/abimanyu.a13.com";
};
 ' > /etc/bind/named.conf.local

service bind9 restart
```

Selanjutnya lakukan stop bind9 pada YudhistiraDNSMaster
> Script dibawah ini terdapat pada root node YudhistiraDNSMaster, untuk menjalankannya bisa langsung dengan melakukan command bash ```no6test.sh```

```bash
echo -e "==================================="
echo "DNS MASTER STOP"
service bind9 stop
echo -e "==================================="
```

Script dibawah ini terdapat pada root node client (NakulaClient & SadewaClient), untuk menjalankannya bisa langsung dengan melakukan command ```bash no6.sh```

```bash
echo nameserver 192.175.2.2  > /etc/resolv.conf
echo nameserver 192.175.2.3  > /etc/resolv.conf

echo -e "==================================="
echo "TES SLAVE DNS, DNS MASTER DI STOP"
ping arjuna.a13.com -c 5
echo -e "==================================="
echo "TES SLAVE DNS, DNS MASTER DI STOP"
ping abimanyu.a13.com -c 5
echo -e "==================================="
```

### Testing

![image](https://github.com/RuleLuluDamara/Jarkom-Modul-2-A13-2023/assets/105763198/911022c9-68e0-4911-85e4-33d43a7318af)

## Question 7
> Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.

### Script

> Script dibawah ini terdapat pada root node YudhistiraDNSMaster, untuk menjalankannya bisa langsung dengan melakukan command bash ```no7.sh```

```bash
echo -e '
$TTL    604800  
@       IN      SOA     abimanyu.a13.com. root.abimanyu.a13.com. (
                        2023100910	; Serial
                        604800	; Refresh
                        86400		; Retry
                        2419200	; Expire
                        604800 )	; Negative Cache TTL
;
@	IN	NS	abimanyu.a13.com.
@       IN	A	192.175.2.2 	;  IP Yudhistira
www	IN      CNAME   abimanyu.a13.com.
parikesit           IN      A       192.175.1.4  ; IP AbimanyuWebServer
www.parikesit  IN      CNAME   parikesit.abimanyu.a07.com.
baratayuda     IN      NS      ns1.abimanyu.a07.com.
ns1            IN      A       192.172.2.3      ; IP Werkudara
' >  /etc/bind/asibkuy/abimanyu.a13.com

echo -e '
options {
        directory "/var/cache/bind";
        allow-query{any;};

        listen-on-v6 { any; };
};
' > /etc/bind/named.conf.options

service bind9 restart

```

> Script dibawah ini terdapat pada root node WerkudaraDNSMaster, untuk menjalankannya bisa langsung dengan melakukan command bash ```no7.sh```

```bash

echo -e '
options {
        directory "/var/cache/bind";

        allow-query{any;};

        listen-on-v6 { any; };
};
' > /etc/bind/named.conf.options


echo -e '
zone "arjuna.a13.com" {
    type slave;
    masters { 192.172.2.2; }; // IP Yudhistira
    file "/var/lib/bind/arjuna.a13.com";
};
zone "abimanyu.a13.com" {  
      	type slave;
       masters {192.175.2.2;};  
    		file "/var/lib/bind/abimanyu.a13.com";
};

zone "baratayuda.abimanyu.a13.com" {
        type master;
        file "/etc/bind/baratayuda/baratayuda.abimanyu.a13.com";
};
 ' > /etc/bind/named.conf.local

// Buat folder baratayuda
mkdir /etc/bind/baratayuda

echo -e '
$TTL    604800
@       IN      SOA     baratayuda.abimanyu.a13.com. root.baratayuda.abimanyu.a13.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       	IN      NS          baratayuda.abimanyu.a13.com.
@       	IN      A           192.175.1.4			; IP Abimanyu
www       	IN      CNAME      baratayuda.abimanyu.a13.com.
' > /etc/bind/baratayuda/baratayuda.abimanyu.a13.com

service bind9 restart

```

Selanjutnya lakukan stop bind9 pada YudhistiraDNSMaster
> Script dibawah ini terdapat pada root node YudhistiraDNSMaster, untuk menjalankannya bisa langsung dengan melakukan command bash ```no7test.sh```

```bash
echo -e "==================================="
echo "DNS MASTER STOP"
service bind9 stop
echo -e "==================================="
```

Script dibawah ini terdapat pada root node client (NakulaClient & SadewaClient), untuk menjalankannya bisa langsung dengan melakukan command ```bash no7.sh```

```bash
echo -e '
nameserver 192.175.2.2
nameserver 192.175.2.3
' > /etc/resolv.conf

echo -e "==================================="
echo "TEST PING DELEGASI SUBDOMAIN (mengarah ke host IP Abimanyu)"
ping baratayuda.abimanyu.a13.com -c 5
echo -e "==================================="
echo "TEST ALIAS (CNAME) (alias dari baratayuda.abimanyu.a13.com)"
host -t CNAME www.baratayuda.abimanyu.a13.com
echo -e "==================================="
```

### Testing

![image](https://github.com/RuleLuluDamara/Jarkom-Modul-2-A13-2023/assets/105763198/99698ea5-3e40-462a-a547-7be9adaa4b80)


## Question 8
> Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.

### Script

> Script dibawah ini terdapat pada root node WerkudaraDNSMaster, untuk menjalankannya bisa langsung dengan melakukan command bash ```no8.sh```

```bash
echo -e '
$TTL    604800
@       IN      SOA     baratayuda.abimanyu.a13.com. root.baratayuda.abimanyu.a13.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       	IN      NS          baratayuda.abimanyu.a13.com.
@       	IN      A           192.175.1.4			; IP Abimanyu
www       	IN      CNAME      baratayuda.abimanyu.a13.com.
rjp                     IN      A       192.175.1.4 ; IP Abimanyu
www.rjp                 IN      CNAME   rjp.baratayuda.abimanyu.a13.com.
' > /etc/bind/baratayuda/baratayuda.abimanyu.a13.com

service bind9 restart

```

Script dibawah ini terdapat pada root node client (NakulaClient & SadewaClient), untuk menjalankannya bisa langsung dengan melakukan command ```bash no8.sh```

```bash
echo -e '
nameserver 192.175.2.2
nameserver 192.175.2.3
' > /etc/resolv.conf


echo -e "==================================="
echo "TEST PING DELEGASI SUBDOMAIN (mengarah ke host IP Abimanyu)"
ping rjp.baratayuda.abimanyu.a13.com -c 5
echo -e "==================================="
echo "TEST ALIAS (CNAME) (alias dari www.rjp.baratayuda.abimanyu.a13.com)"
host -t CNAME www.rjp.baratayuda.abimanyu.a13.com
echo -e "==================================="

```

### Testing

![image](https://github.com/RuleLuluDamara/Jarkom-Modul-2-A13-2023/assets/105763198/4766bb33-5096-4591-9bac-db730cf9e0e5)

## Question 9 
> Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.

### Script 

> Script dibawah ini terdapat pada root node PrabukusumaWebServer, untuk menjalankannya bisa langsung dengan melakukan command bash ```no9.sh```

```bash
apt-get update
apt install nginx php php-fpm -y

mkdir /var/www/asibkuy

echo '<?php
echo "Hi this is message PrabukusumaWebServer";
?>' > /var/www/asibkuy/index.php

echo '
 server {
        listen 80;

        root /var/www/asibkuy;

        index index.php index.html index.htm;
        server_name _;


        location / {
                        try_files $uri $uri/ /index.php?$query_string;
        }

        # pass PHP scripts to FastCGI server
        location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
        }


 location ~ /\.ht {
                        deny all;
        }

        error_log /var/log/nginx/asibkuy_error.log;
        access_log /var/log/nginx/asibkuy_access.log;
 }
' > /etc/nginx/sites-available/asibkuy


ln -s /etc/nginx/sites-available/asibkuy /etc/nginx/sites-enabled

rm /etc/nginx/sites-enabled/default

service nginx restart
service php7.2-fpm stop
service php7.2-fpm start
```

> Script dibawah ini terdapat pada root node AbimanyuWebServer, untuk menjalankannya bisa langsung dengan melakukan command bash ```no9.sh```

```bash
apt install nginx php php-fpm -y


# Buat folder jarkom
mkdir /var/www/asibkuy


echo '<?php
echo "Hi this is message AbimanyuWebServer";
?>' > /var/www/asibkuy/index.php


echo '
 server {
        listen 80;

        root /var/www/asibkuy;

        index index.php index.html index.htm;
        server_name _;

        location / {
                        try_files $uri $uri/ /index.php?$query_string;
        }

        # pass PHP scripts to FastCGI server
        location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
        }


 location ~ /\.ht {
                        deny all;
        }


        error_log /var/log/nginx/jarkom_error.log;
        access_log /var/log/nginx/jarkom_access.log;
 }
' > /etc/nginx/sites-available/asibkuy

ln -s /etc/nginx/sites-available/asibkuy /etc/nginx/sites-enabled


rm /etc/nginx/sites-enabled/default

service nginx restart
service php7.2-fpm stop
service php7.2-fpm start
```

> Script dibawah ini terdapat pada root node WisanggeniWebServer, untuk menjalankannya bisa langsung dengan melakukan command bash ```no9.sh```

```bash
apt install nginx php php-fpm -y


# Buat folder jarkom
mkdir /var/www/asibkuy


echo '<?php
echo "Hi this is message WisanggeniWebServer";
?>' > /var/www/asibkuy/index.php


echo '
 server {
        listen 80;

        root /var/www/asibkuy;

        index index.php index.html index.htm;
        server_name _;

        location / {
                        try_files $uri $uri/ /index.php?$query_string;
        }

        # pass PHP scripts to FastCGI server
        location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
        }


 location ~ /\.ht {
                        deny all;
        }


        error_log /var/log/nginx/jarkom_error.log;
        access_log /var/log/nginx/jarkom_access.log;
 }
' > /etc/nginx/sites-available/asibkuy

ln -s /etc/nginx/sites-available/asibkuy /etc/nginx/sites-enabled


rm /etc/nginx/sites-enabled/default

service nginx restart
service php7.2-fpm stop
service php7.2-fpm start
```

> Script dibawah ini terdapat pada root node ArjunaLoadBalancer, untuk menjalankannya bisa langsung dengan melakukan command bash ```no9.sh```

```bash
apt-get update

# Install bind9
apt-get install bind9 nginx -y

echo '
 upstream myweb  {
  server 192.175.1.5 #IP Prabukusuma
  server 192.175.1.4 #IP Abimanyu
  server 192.175.1.6#IP Wisanggeni
 }


 server {
  listen 80;
  server_name arjuna.a13.com www.arjuna.a13.com;


  location / {
  proxy_pass http://myweb;
  }
 }' > /etc/nginx/sites-available/lb-asibkuy


ln -s /etc/nginx/sites-available/lb-asibkuy /etc/nginx/sites-enabled

service nginx restart

```

### Testing

![image](https://github.com/RuleLuluDamara/Jarkom-Modul-2-A13-2023/assets/105763198/0ecf3ada-11d7-4d9e-8c7b-9f4a8abf3e2a)


## Question 10
> Kemudian gunakan algoritma Round Robin untuk Load Balancer pada Arjuna. Gunakan server_name pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh
    - Prabakusuma:8001
    - Abimanyu:8002
    - Wisanggeni:8003

### Script 

Pertama-tama, merubah konfigurasi file ```/etc/nginx/sites-available/asibkuy``` pada node PrabukusumaWebServer.
> Script dibawah ini terdapat pada root node PrabukusumaWebServer, untuk menjalankannya bisa langsung dengan melakukan command bash ```no10.sh```

```bash
echo '
 server {
        listen 8002;

        root /var/www/asibkuy;


        index index.php index.html index.htm;
        server_name _;


        location / {
                        try_files $uri $uri/ /index.php?$query_string;
        }


        # pass PHP scripts to FastCGI server
        location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
        }


 location ~ /\.ht {
                        deny all;
        }


        error_log /var/log/nginx/asibkuy_error.log;
        access_log /var/log/nginx/asibkuy_access.log;
 }
' > /etc/nginx/sites-available/asibkuy
```

> Script dibawah ini terdapat pada root node ArjunaLoadBalancer, untuk menjalankannya bisa langsung dengan melakukan command bash ```no10.sh```

```bash
apt-get update

# Install bind9
apt-get install bind9 nginx -y

echo ' # Default menggunakan Round Robin
 upstream myweb  {
  server 192.175.1.5:8001; #IP Prabukusuma
  server 192.175.1.4:8002; #IP Abimanyu
  server 192.175.1.6:8003; #IP Wisanggeni
 }


 server {
  listen 80;
  server_name arjuna.a13.com www.arjuna.a13.com;


  location / {
  proxy_pass http://myweb;
  }
 }' > /etc/nginx/sites-available/lb-asibkuy


ln -s /etc/nginx/sites-available/lb-asibkuy /etc/nginx/sites-enabled


service nginx restart
```


