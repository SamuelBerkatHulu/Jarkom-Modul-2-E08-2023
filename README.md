# Jarkom-Modul-2-E08-2023
Laporan Resmi Praktimum 2 JARKOM

**Anggota Kelompok ``E08``** 
| No | Nama | NRP |
| --- | --- | --- |
| 1 | Samuel Berkat Hulu | 5025201055 |
| 2 | Hanafi Satriyo Utomo Setiawan | 5025211195 |

## Soal 1
Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian sebagai berikut. Folder topologi dapat diakses pada drive berikut 

## Penyelesaian Soal 1
![07](https://github.com/SamuelBerkatHulu/Jarkom-Modul-2-E08-2023/assets/100474007/f9815da9-e216-4719-8405-1df19401fbdf)



## Soal 2
Buatlah website utama pada node arjuna dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok.

## Penyelesaian Soal 2
```R
#------------------------------ Nomor 2 ------------------------------
#Yudhistira <-------------!
#!/bin/bash

apt-get update
apt-get install bind9 -y

echo '
zone "arjuna.e08.com" {
        type master;
        file "/etc/bind/jarkom/arjuna.e08.com";
};' > /etc/bind/named.conf.local

mkdir -p /etc/bind/jarkom
cp /etc/bind/db.local /etc/bind/jarkom/arjuna.e08.com

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     arjuna.e08.com. root.arjuna.e08.com. (
                     2023101001         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      arjuna.e08.com.
@       IN      A       192.210.1.4     ; IP Arjuna
www     IN      CNAME   arjuna.e08.com.
@       IN      AAAA    ::1' > /etc/bind/jarkom/arjuna.e08.com

service bind9 restart

#Nakula <-------------!
#!/bin/bash

echo 'nameserver 192.210.2.3    # IP DNSMaster' >  /etc/resolv.conf
```


## Soal 3
Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com.

## Penyelesaian Soal 3
```R
#------------------------------ Nomor 3 ------------------------------

#Yudhistira <-------------!
#!/bin/bash

echo '
zone "abimanyu.e08.com" {
        type master;
        file "/etc/bind/jarkom/abimanyu.e08.com";
};' >> /etc/bind/named.conf.local

cp /etc/bind/db.local /etc/bind/jarkom/abimanyu.e08.com

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.e08.com. root.abimanyu.e08.com. (
                     2023101001         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      abimanyu.e08.com.
@       IN      A       192.210.3.3     ; IP Abimanyu
www     IN      CNAME   abimanyu.e08.com.
@       IN      AAAA    ::1' > /etc/bind/jarkom/abimanyu.e08.com

service bind9 restart
```

## Soal 4
Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.

## Penyelesaian Soal 4
```R
#------------------------------ Nomor 4 ------------------------------

#Yudhistira <-------------!
#!/bin/bash

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.e08.com. root.abimanyu.e08.com. (
                     2023101001         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      abimanyu.e08.com.
@       IN      A       192.210.3.3     ; IP Abimanyu
www     IN      CNAME   abimanyu.e08.com.
parikesit IN    A       192.210.3.3     ; IP Abimanyu
@       IN      AAAA    ::1
' > /etc/bind/jarkom/abimanyu.e08.com

service bind9 restart
```


## Soal 5
Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse)

## Penyelesaian Soal 5
```R
#------------------------------ Nomor 5 ------------------------------

#Yudhistira <-------------!
#!/bin/bash

echo '
zone "3.210.192.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/3.210.192.in-addr.arpa";
};' >> /etc/bind/named.conf.local

cp /etc/bind/db.local /etc/bind/jarkom/3.210.192.in-addr.arpa

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.e08.com. root.abimanyu.e08.com. (
                     2023101001         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
3.210.192.in-addr.arpa. IN      NS      abimanyu.e08.com.
3                       IN      PTR     abimanyu.e08.com.       ; Byte ke-4 IP Abimanyu' > /etc/bind/jarkom/3.210.192.in-addr.arpa

service bind9 restart

#Nakula <-------------!
#!/bin/bash

echo 'nameserver 192.168.122.1' > /etc/resolv.conf
apt-get update
apt-get install dnsutils

echo 'nameserver 192.210.2.3' > /etc/resolv.conf
host -t PTR 192.210.3.3
```

## Soal 6
Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.

## Penyelesaian Soal 6
```R
#------------------------------ Nomor 6 ------------------------------

#Yudhistira <-------------!
#!/bin/bash

echo '
zone "arjuna.e08.com" {
        type master;
        also-notify { 192.210.2.2; }; // IP WerkudaraDNSSlave
        allow-transfer { 192.210.2.2; }; // IP WerkudaraDNSSlave
        file "/etc/bind/jarkom/arjuna.e08.com";
};

zone "abimanyu.e08.com" {
    type master;
    notify yes;
    also-notify { 192.210.2.2; }; // IP WerkudaraDNSSlave
    allow-transfer { 192.210.2.2; }; // IP WerkudaraDNSSlave
    file "/etc/bind/jarkom/abimanyu.e08.com";
};

zone "2.210.192.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/2.210.192.in-addr.arpa";
};' > /etc/bind/named.conf.local

service bind9 restart

���
#Werkudara <-------------!
#!/bin/bash

apt-get update
apt-get install bind9 -y

echo '
zone "arjuna.e08.com" {
    type slave;
    masters { 192.210.2.3; }; // IP Yudhis
    file "/var/lib/bind/arjuna.e08.com";
};

zone "abimanyu.e08.com" {
    type slave;
    masters { 192.210.2.3; }; // IP Yudhis
    file "/var/lib/bind/abimanyu.e08.com";
};
' > /etc/bind/named.conf.local

service bind9 restart



���
#Nakula <-------------!
#!/bin/bash

echo '
nameserver 192.210.2.2  # IP Yudhistira
nameserver 192.210.2.3  # IP Werkudara
' > /etc/resolv.conf
```

## Soal 7
Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.

## Penyelesaian Soal 7
```R
#------------------------------ Nomor 7 ------------------------------
#Yudhistira
#!/bin/bash

apt-get update
apt-get install bind9 -y

echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.e08.com. root.abimanyu.e08.com. (
                     2023101201         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      abimanyu.e08.com.
@       IN      A       192.210.3.3     ; IP Abimanyu
www     IN      CNAME   abimanyu.e08.com.
parikesit IN    A       192.210.3.3     ; IP Abimanyu
ns1     IN      A       192.210.2.2     ; IP Werkudara
baratayuda IN   NS      ns1
@       IN      AAAA    ::1' > /etc/bind/jarkom/abimanyu.e08.com

service bind9 restart

echo 'options {
       directory "/var/cache/bind";

       // If there is a firewall between you and nameservers you want
       // to talk to, you may need to fix the firewall to allow multiple
       // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

       // If your ISP provided one or more IP addresses for stable
       // nameservers, you probably want to use them as forwarders.
       // Uncomment the following block, and insert the addresses replacing
       // the all-0'\''s placeholder.
       // forwarders {
       //      0.0.0.0;
       // };

       //========================================================================
       // If BIND logs error messages about the root key being expired,
       // you will need to update your keys.  See https://www.isc.org/bind-keys
       //========================================================================
       //dnssec-validation auto;
       allow-query{any;};

       auth-nxdomain no;    # conform to RFC1035
       listen-on-v6 { any; };
};' > /etc/bind/named.conf.options

echo '
zone "arjuna.e08.com" {
       type master;
       file "/etc/bind/jarkom/arjuna.e08.com";
};

zone "abimanyu.e08.com" {
    type master;
    file "/etc/bind/jarkom/abimanyu.e08.com";
    allow-transfer { 192.210.2.2; }; // IP Werkudara
};

zone "2.210.192.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/2.210.192.in-addr.arpa";
};' > /etc/bind/named.conf.local

service bind9 restart



���
Werkudara
#!/bin/bash

apt-get update
apt-get install bind9 -y

echo 'options {
        directory "/var/cache/bind";

        // If there is a firewall between you and nameservers you want
        // to talk to, you may need to fix the firewall to allow multiple
        // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

        // If your ISP provided one or more IP addresses for stable
        // nameservers, you probably want to use them as forwarders.
        // Uncomment the following block, and insert the addresses replacing
        // the all-0'\''s placeholder.

        // forwarders {
        //      0.0.0.0;
        // };

        //========================================================================
        // If BIND logs error messages about the root key being expired,
        // you will need to update your keys.  See https://www.isc.org/bind-keys
        //========================================================================
        //dnssec-validation auto;
        allow-query{any;};

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};' > /etc/bind/named.conf.options

echo '
zone "arjuna.e08.com" {
    type slave;
    masters { 192.210.2.3; }; // IP Yudhis
    file "/var/lib/bind/arjuna.e08.com";
};

zone "abimanyu.e08.com" {
    type slave;
    masters { 192.210.2.3; }; // IP Yudhis
    file "/var/lib/bind/abimanyu.e12.com";
};

zone "baratayuda.abimanyu.e08.com" {
    type master;
    file "/etc/bind/baratayuda/baratayuda.abimanyu.e08.com";
};' > /etc/bind/named.conf.local

mkdir -p /etc/bind/baratayuda
cp /etc/bind/db.local /etc/bind/baratayuda/baratayuda.abimanyu.e08.com

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     baratayuda.abimanyu.e08.com. root.baratayuda.abimanyu.e08.com. (
                     2023101201         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      baratayuda.abimanyu.e08.com.
@       IN      A       192.210.3.3     ; IP Abimanyu
www     IN      CNAME   baratayuda.abimanyu.e08.com.' > /etc/bind/baratayuda/baratayuda.abimanyu.e08.com

service bind9 restart
```


## Soal 8
Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.

## Penyelesaian Soal 8
```R
#------------------------------ Nomor 8 ------------------------------

#Werkudara
#!/bin/bash

echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     baratayuda.abimanyu.e08.com. root.baratayuda.abimanyu.e08.com. (
                     2023101001         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      baratayuda.abimanyu.e08.com.
@       IN      A       192.210.3.3     ; IP Abimanyu
www     IN      CNAME   baratayuda.abimanyu.e08.com.
rjp     IN      A       192.210.3.3     ; IP Abimanyu
www.rjp IN      CNAME   rjp.baratayuda.abimanyu.e08.com.' > /etc/bind/baratayuda/baratayuda.abimanyu.e08.com

service bind9 restart
```


## Soal 9
Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.

## Penyelesaian Soal 9
```R
#------------------------------ Nomor 9 ------------------------------

#Arjuna<---------------
#!/bin/bash

apt-get update
apt-get install bind9 nginx

echo '
#Menggunakan Round Robin
upstream myweb  {
    server 192.210.3.2; #IP Prabukusuma
    server 192.210.3.3; #IP Abimanyu
    server 192.210.3.4; #IP Wisanggeni
}

server {
    listen 80;
    server_name arjuna.e08.com www.arjuna.e08.com;

    location / {
        proxy_pass http://myweb;
    }
}' > /etc/nginx/sites-available/lb-jarkom

ln -s /etc/nginx/sites-available/lb-jarkom /etc/nginx/sites-enabled

service nginx restart
���
#Prabukusuma<-----------------
#!/bin/bash

apt-get update && apt install nginx php php-fpm -y

mkdir -p /var/www/jarkom

echo '<?php
 echo "Halo, Kamu berada di Prabukusuma";
 ?>' > /var/www/jarkom/index.php

echo '
 server {

        listen 80;

        root /var/www/jarkom/;

        index index.php index.html index.htm;
        server_name _;

        location / {
                        try_files $uri $uri/ /index.php?$query_string;
        }

        # pass PHP scripts to FastCGI server
        location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
        }

 location ~ /\.ht {
                        deny all;
        }

        error_log /var/log/nginx/jarkom_error.log;
        access_log /var/log/nginx/jarkom_access.log;
 }' > /etc/nginx/sites-available/jarkom

ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled
rm -rf /etc/nginx/sites-enabled/default
service php7.0-fpm start
service php7.0-fpm restart
service nginx restart

#Abimanyu<-------------------
#!/bin/bash

apt-get update && apt install nginx php php-fpm -y
mkdir -p /var/www/jarkom

echo '<?php
 echo "Halo, Kamu berada di Abimanyu";
 ?>' > /var/www/jarkom/index.php

echo '
 server {

        listen 80;

        root /var/www/jarkom;

        index index.php index.html index.htm;
        server_name _;

        location / {
                        try_files $uri $uri/ /index.php?$query_string;
        }

        # pass PHP scripts to FastCGI server
        location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
        }

location ~ /\.ht {
                        deny all;
        }

        error_log /var/log/nginx/jarkom_error.log;
        access_log /var/log/nginx/jarkom_access.log;
 }' > /etc/nginx/sites-available/jarkom

ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled
rm -rf /etc/nginx/sites-enabled/default
service php7.0-fpm start
service php7.0-fpm restart
service nginx restart

#Wisanggeni<---------------------!
#!/bin/bash

apt-get update && apt install nginx php php-fpm -y
mkdir -p /var/www/jarkom

echo '<?php
 echo "Halo, Kamu berada di Wisanggeni";
 ?>' > /var/www/jarkom/index.php

echo '
 server {

        listen 80;

        root /var/www/jarkom;

        index index.php index.html index.htm;
        server_name _;

        location / {
                        try_files $uri $uri/ /index.php?$query_string;
        }

        # pass PHP scripts to FastCGI server
        location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
        }

location ~ /\.ht {
                        deny all;
        }

        error_log /var/log/nginx/jarkom_error.log;
        access_log /var/log/nginx/jarkom_access.log;
 }' > /etc/nginx/sites-available/jarkom

ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled
rm -rf /etc/nginx/sites-enabled/default
service php7.0-fpm start
service php7.0-fpm restart
service nginx restart���
```


## Soal 10
Kemudian gunakan algoritma Round Robin untuk Load Balancer pada Arjuna. Gunakan server_name pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh
    - Prabakusuma:8001
    - Abimanyu:8002
    - Wisanggeni:8003

## Penyelesaian Soal 10
```R
#------------------------------ Nomor 10 ------------------------------

#Arjuna<------------------!
#!/bin/bash

echo '
 # Default menggunakan Round Robin
 upstream myweb  {
        server 192.210.3.2:8001; #IP Prabukusuma
        server 192.210.3.3:8002; #IP Abimanyu
        server 192.210.3.4:8003; #IP Wisanggeni
 }

 server {
        listen 80;
        server_name arjuna.e08.com www.arjuna.e08.com;

        location / {
        proxy_pass http://myweb;
        }
 }' > /etc/nginx/sites-available/lb-jarkom

 ln -s /etc/nginx/sites-available/lb-jarkom /etc/nginx/sites-enabled

service nginx restart
���
#Prabukusuma<--------------------!
#!/bin/bash

echo '
 server {

        listen 8001;

        root /var/www/jarkom/;

        index index.php index.html index.htm;
        server_name _;

        location / {
                        try_files $uri $uri/ /index.php?$query_string;
        }

        # pass PHP scripts to FastCGI server
        location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
        }

 location ~ /\.ht {
                        deny all;
        }

        error_log /var/log/nginx/jarkom_error.log;
        access_log /var/log/nginx/jarkom_access.log;
 }' > /etc/nginx/sites-available/jarkom

ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled

service php7.0-fpm start
service php7.0-fpm restart
service nginx restart
���
#Abimanyu<-------------------!
#!/bin/bash

echo '
 server {

        listen 8002;

        root /var/www/jarkom/;

        index index.php index.html index.htm;
        server_name _;

        location / {
                        try_files $uri $uri/ /index.php?$query_string;
        }

        # pass PHP scripts to FastCGI server
        location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
        }

 location ~ /\.ht {
                        deny all;
        }

        error_log /var/log/nginx/jarkom_error.log;
        access_log /var/log/nginx/jarkom_access.log;
 }' > /etc/nginx/sites-available/jarkom

ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled

service php7.0-fpm start
service php7.0-fpm restart
service nginx restart
���
#Wisanggeni<---------------------!
#!/bin/bash

echo '
 server {

        listen 8003;

        root /var/www/jarkom/;

        index index.php index.html index.htm;
        server_name _;

        location / {
                        try_files $uri $uri/ /index.php?$query_string;
        }

        # pass PHP scripts to FastCGI server
        location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
        }

 location ~ /\.ht {
                        deny all;
        }

        error_log /var/log/nginx/jarkom_error.log;
        access_log /var/log/nginx/jarkom_access.log;
 }' > /etc/nginx/sites-available/jarkom

ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled

service php7.0-fpm start
service php7.0-fpm restart
```


## Soal 10
Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server www.abimanyu.yyy.com. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy
Setelah itu ubahlah agar url www.abimanyu.yyy.com/index.php/home menjadi 

## Penyelesaian Soal 10

Pertama buat dulu masuk ke ``cd /etc/apache2/sises-available`` kemduian cp file ``000-default.conf`` menjadi ``abimanyu.conf``
denga cara ``cp 000-default.conf abimanyu.conf`` catata : saya berada di folder ``etc/apache2/sites-avilable``.

kemudian buka file  ``nano abimanyu.conf`` dan edit seperti berikut :
![konfigurasi Webserver dari Abimanyu](https://github.com/SamuelBerkatHulu/Jarkom-Modul-2-E08-2023/assets/100474007/04715f1f-5ff6-4c12-97b3-902aa0e68b48)
```R
echo '
<VirtualHost *:80>


  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/abimanyu.e08.com
  ServerName abimanyu.e08.com
  ServerAlias www.abimanyu.e08.com

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined


</VirtualHost>' 

service apache2 start
a2ensite abimanyu.conf
service apache2 restart
```

untuk melakukan chek koneksi masuk ke ``cd /var/www.abimanyu.e08.com`` dan buat file ``index.php`` 
```R
<?php
        echo "Halo Salam Kenal";
?>
```
dan pada clinet ``NekulaClient`` gunakan lynx untuk akses ``lynx abimanyu.e08.com/index.php`` 
![cek webserver settignan awal](https://github.com/SamuelBerkatHulu/Jarkom-Modul-2-E08-2023/assets/100474007/d57394e4-0f53-4fe0-9951-7c4397daa975)

