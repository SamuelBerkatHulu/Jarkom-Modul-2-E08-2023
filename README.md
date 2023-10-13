# Jarkom-Modul-2-E08-2023
Laporan Resmi Praktimum 2 JARKOM

**Anggota Kelompok ``E08``** 
| No | Nama | NRP |
| --- | --- | --- |
| 1 | Samuel Berkat Hulu | 5025201055 |
| 2 | Hanafi Satriyo Utomo Setiawan | 5025211195 |

## Soal 1
Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian sebagai berikut. Folder topologi dapat diakses pada drive berikut 
![07](https://github.com/SamuelBerkatHulu/Jarkom-Modul-2-E08-2023/assets/100474007/f9815da9-e216-4719-8405-1df19401fbdf)

## Penyelesaian Soal 1
```R
Router 
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.210.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.210.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 192.210.3.1
	netmask 255.255.255.0


Switch 1
NekulaClient 
auto eth0
iface eth0 inet static
	address 192.210.1.2
	netmask 255.255.255.0
	gateway 192.210.1.1
SadewaClient
auto eth0
iface eth0 inet static
	address 192.210.1.3
	netmask 255.255.255.0
	gateway 192.210.1.1
ArjunaLoadBalancer
auto eth0
iface eth0 inet static
	address 192.210.1.4
	netmask 255.255.255.0
	gateway 192.210.1.1

Switch 2
WekudaraDNSSlave 
auto eth0
iface eth0 inet static
	address 192.210.2.3
	netmask 255.255.255.0
	gateway 192.210.2.1
YudhistiraDNSMaster
auto eth0
iface eth0 inet static
	address 192.210.2.2
	netmask 255.255.255.0
	gateway 192.210.2.1
Switch 3
PrabukusumaWebServer
auto eth0
iface eth0 inet static
	address 192.210.3.2
	netmask 255.255.255.0
	gateway 192.210.3.1
AbimanyuWebServer
auto eth0
iface eth0 inet static
	address 192.210.3.3
	netmask 255.255.255.0
	gateway 192.210.3.1
WisanggeniWebServer
auto eth0
iface eth0 inet static
	address 192.210.3.4
	netmask 255.255.255.0
	gateway 192.210.3.1


Konfigurasi Script Shell Tiap Node : 
Router : 
nano awal.sh >> pada directori root
#!/bin/bash
#iptables distribusi jaringan
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.210.0.0/16
#name server
cat /etc/resolv.conf
#update
apt-get update
Switch 1 : 

```


## Soal 2
Buatlah website utama pada node arjuna dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok.

## Penyelesaian Soal 2


## Soal 3
Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com.
## Penyelesaian Soal 3


## Soal 4
Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.

## Penyelesaian Soal 4


## Soal 5
Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse)

## Penyelesaian Soal 5


## Soal 6
Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.

## Penyelesaian Soal 6


## Soal 7
Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.

## Penyelesaian Soal 7



## Soal 8
Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.

## Penyelesaian Soal 8



## Soal 9
Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.

## Penyelesaian Soal 9



## Soal 10
Kemudian gunakan algoritma Round Robin untuk Load Balancer pada Arjuna. Gunakan server_name pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh
    - Prabakusuma:8001
    - Abimanyu:8002
    - Wisanggeni:8003

## Penyelesaian Soal 10



