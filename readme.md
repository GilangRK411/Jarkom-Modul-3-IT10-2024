# Jarkom-Modul-3-IT10-2024


# Anggota Kelompok
| Nama | NRP |
| ---------------------- | ---------- |
| Khansa Adia Rahma      | 5027221071 |
| Gilang Raya Kurnaiwan   | 5027221045 |

# Topologi
Pertama buat Topologi sebagai berikut:
![alt text](pict/topologi.png)

# Konfigurasi
Edit network configuration sebagai berikut:
- Arakis (DHCP Relay)
![alt text](pict/configArakis.png)
lengkapnya sebagai berikut:
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
    address 192.238.1.1
    netmask 255.255.255.0

auto eth2
iface eth2 inet static
    address 192.238.2.1
    netmask 255.255.255.0

auto eth3
iface eth3 inet static
    address 192.238.3.1
    netmask 255.255.255.0

auto eth4
iface eth4 inet static
    address 192.238.4.1
    netmask 255.255.255.0
```

- Dmitri (Client)
```
auto eth0
iface eth0 inet static
  address 192.238.1.2
  netmask 255.255.255.0
  gateway 192.238.1.1
```

- Vladimir (Worker PHP)
```
auto eth0
iface eth0 inet static
  address 192.238.1.3
  netmask 255.255.255.0
  gateway 192.238.1.1
```

- Rabban (Worker PHP)
```
auto eth0
iface eth0 inet static
  address 192.238.1.4
  netmask 255.255.255.0
  gateway 192.238.1.1
```

- Feyd (Worker PHP)
```
auto eth0
iface eth0 inet static
  address 192.238.1.5
  netmask 255.255.255.0
  gateway 192.238.1.1
```

- irulan (DNS Server)
```
auto eth0
iface eth0 inet static
  address 192.238.3.2
  netmask 255.255.255.0
  gateway 192.238.3.1
```

- Mohiam (DHCP Server)
```
auto eth0
iface eth0 inet static
  address 192.238.3.3
  netmask 255.255.255.0
  gateway 192.238.3.1
```

- chani (Database Server)
```
auto eth0
iface eth0 inet static
  address 192.238.4.2
  netmask 255.255.255.0
  gateway 192.238.4.1
```

- stilgar (Load Balancer)
```
auto eth0
iface eth0 inet static
  address 192.238.4.3
  netmask 255.255.255.0
  gateway 192.238.4.1
```

- paul (Client)
```
auto eth0
iface eth0 inet static
  address 192.238.2.2
  netmask 255.255.255.0
  gateway 192.238.2.1
```

- leto (Worker Laravel)
```
auto eth0
iface eth0 inet static
  address 192.238.2.3
  netmask 255.255.255.0
  gateway 192.238.2.1
```

- duncan (Worker Laravel)
```
auto eth0
iface eth0 inet static
  address 192.238.2.4
  netmask 255.255.255.0
  gateway 192.238.2.1
```

- jessica (Worker Laravel)
```
auto eth0
iface eth0 inet static
  address 192.238.2.5
  netmask 255.255.255.0
  gateway 192.238.2.1
```

Selanjutnya, pada root tambahkan juga script berikut

1. pada nodes Arakis
```
cd
nano script.sh
```
tambahkan ke dalam script.sh dengan isi sebagai berikut:
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.238.0.0/16
echo nameserver 192.168.122.1 > /etc/resolv.conf
```

untuk menjalankannya: ``bash script.sh``

apabila terdapat pertanyaan seperti dibawah ini, bisa dijawab dengan: 
![alt text](pict/forwardreq.png)
```
# What servers should the DHCP relay forward requests to?
SERVERS="192.238.3.3"

# On what interfaces should the DHCP relay (dhrelay) serve DHCP requests?
INTERFACES="eth0 eth1 eth2 eth3 eth4"

# Additional options that are passed to the DHCP relay daemon?
OPTIONS=""
```

2. Pada semua nodes lakukan berikut ini:
``nano script.sh``
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
```
tambahkan juga script instalasi berikut pada node:
- *Mohiam* (DHCP Server): ``apt-get install isc-dhcp-server``
- *Irulan* (DNS Server): ``apt-get install bind9``
- *Arakis* (DHCP Relay): ``apt-get install isc-dhcp-relay``
- *Vladimir, Rabban, Feyd* (Worker PHP): ``apt-get install php7.3``
- *Stilgar* (Load Balancer): 
```
apt-get install nginx
apt-get install apache2-utils
```
- *Chani* (Database Server): ``apt-get install mysql-server``
- *Leto, Duncan, Jessica* (Worker Laravel): 
```
apt-get install php8.0
apt-get install composer
```
# No 1. 
Untuk mengatur agar semua client *Dmitri dan Paul* menggunakan konfigurasi dari DHCP Server *Mohiam*

Bagian ini sudah termasuk di config sebelumnya, yaitu
```
echo "auto eth0
iface eth0 inet dhcp" > /etc/network/interfaces.d/eth0
```
Sehingga client Dmitri dan Paul akan secara otomatis meminta IP address dari DHCP Server (Mohiam) ketika startup atau restart jaringan.

# No 2,3,5.
Client yang melalui House Harkonen mendapatkan range IP dari [prefix IP].1.14 - [prefix IP].1.28 dan [prefix IP].1.49 - [prefix IP].1.70 (2)

Client yang melalui House Atreides mendapatkan range IP dari [prefix IP].2.15 - [prefix IP].2.25 dan [prefix IP].2 .200 - [prefix IP].2.210 (3)

Durasi DHCP server meminjamkan alamat IP kepada Client yang melalui House Harkonen selama 5 menit sedangkan pada client yang melalui House Atreides selama 20 menit. Dengan waktu maksimal dialokasikan untuk peminjaman alamat IP selama 87 menit (5)
*house == switch

- Pada script.sh di root Arakis
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install isc-dhcp-relay

service isc-dhcp-relay start

echo -e 'SERVERS="192.238.3.3"
INTERFACES="eth0 eth1 eth2 eth3"
OPTIONS='> /etc/default/isc-dhcp-relay 
```

jalankan ``bash script.sh``
![alt text](pict/RunArakis.png)

- pada DHCP Server yaitu *Mohiam*, tambahkan konfigurasi berikut ke dalam file ``/etc/dhcp/dhcpd.conf`` untuk mengatur rentang IP Sesuai dengan ketentuan:

```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt update
apt install isc-dhcp-server -y

# Set interface untuk DHCP Server
echo 'INTERFACESv4="eth0"' > /etc/default/isc-dhcp-server

# Konfigurasi DHCP Server
# DHCP Server Configuration file.
echo 'subnet 192.238.3.0 netmask 255.255.255.0 {
}

# Konfigurasi subnet untuk House Harkonen
subnet 192.238.1.0 netmask 255.255.255.0 {
    range 192.238.1.14 192.238.1.28;
    range 192.238.1.49 192.238.1.70;
    option routers 192.238.1.1;
    option domain-name-servers 192.238.3.2;
    default-lease-time 300; # 5 minutes
    max-lease-time 5220; # 87 minutes
}

# Konfigurasi subnet untuk House Atreides
subnet 192.238.2.0 netmask 255.255.255.0 {
    range 192.238.2.15 192.238.2.25;
    range 192.238.2.200 192.238.2.210;
    option routers 192.238.2.1;
    option domain-name-servers 192.238.3.2;
    default-lease-time 1200; # 20 minutes
    max-lease-time 5220; # 87 minutes
}
' > /etc/dhcp/dhcpd.conf

service isc-dhcp-server start
service isc-dhcp-server status

```
jalankan ``./script.sh``
![alt text](pict/runmohiam.png)

# No 4.
Client mendapatkan DNS dari Princess Irulan dan dapat terhubung dengan internet melalui DNS tersebut. 

pada *irulan*, atur script sbg berikut
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
# Instalasi DNS Server
apt update
apt install bind9 -y
apt install dnsutils -y

# Konfigurasi DNS Server
echo "
options {
    directory \"/var/cache/bind\";

    forwarders {
        192.168.122.1
    };

    dnssec-validation auto;

    listen-on-v6 { any; };
};
" > /etc/bind/named.conf.options

echo "
zone \"atreides.it10.com\" {
    type master;
    file \"/etc/bind/db.atreides.it10.com\";
};

zone \"harkonen.it10.com\" {
    type master;
    file \"/etc/bind/db.harkonen.it10.com\";
};
" > /etc/bind/named.conf.local

echo "
\$TTL    604800
@       IN      SOA     ns.atreides.it10.com. root.atreides.it10.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      ns.atreides.it10.com.
ns      IN      A       192.238.3.2
leto    IN      A       192.238.2.3
duncan  IN      A       192.238.2.4
jessica IN      A       192.238.2.5
" > /etc/bind/db.atreides.it10.com

echo "
\$TTL    604800
@       IN      SOA     ns.harkonen.it10.com. root.harkonen.it10.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      ns.harkonen.it10.com.
ns      IN      A       192.238.3.2
vladimir IN     A       192.238.1.3
rabban  IN      A       192.238.1.4
feyd    IN      A       192.238.1.5
" > /etc/bind/db.harkonen.it10.com

# Restart DNS Server
service bind9 restart
```
jalankan ``./script.sh``
![alt text](pict/runirulan.png)

## Verifikasi DNS Server di Irulan:
```
dig @localhost atreides.it10.com
dig @localhost harkonen.it10.com
```
![alt text](pict/digatreides.png)
![alt text](pict/digharkonen.png)

## Tes Resolusi DNS dengan nslookup:
``nslookup mydomain.com 192.238.3.2``
![alt text](pict/nslookup.png)

# No 6.
Vladimir Harkonen memerintahkan setiap worker(harkonen) PHP, untuk melakukan konfigurasi virtual host untuk website berikut dengan menggunakan php 7.3.

Pada masing-masing worker Harkonen (Vladimir, Rabban, dan Feyd):
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install nginx -y
apt-get install wget -y
apt-get install unzip -y
apt-get install lynx -y
apt-get install htop -y
apt-get install apache2-utils -y
apt-get install php7.3-fpm php7.3-common php7.3-mysql php7.3-gmp php7.3-curl ph$

wget -O '/var/www/harkonen.it10.com' 'https://drive.google.com/file/d/1lmnXJUbyx1JDt2OA5z_1dEowxozfkn30/view?usp=sharing’

unzip -o /var/www/harkonen.it10.com  -d /var/www/

cp /etc/nginx/sites-available/default /etc/nginx/sites-available/harkonen.it10.com

# Konfigurasi Virtual Host
echo 'server {
    listen 80;
    server_name _;

    root /var/www/harkonen;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.3-fpm.sock;  # Sesuaikan versi PHP dan $
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}' > /etc/nginx/sites-available/harkonen.it10.com

service php7.3-fpm start
```
jalankan dengan ``bash script.sh`` pada *Vladimir, Rabban, dan Feyd*
![alt text](pict/konfigvladimir.png)

Nantinya akan muncul seperti berikut:
![alt text](pict/webconfig.png)

# No 7.
Aturlah agar Stilgar dari fremen dapat dapat bekerja sama dengan maksimal, lalu lakukan testing dengan 5000 request dan 150 request/second.
