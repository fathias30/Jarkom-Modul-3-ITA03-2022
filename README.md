# Jarkom-Modul-3-ITA03-2022

## Anggota Kelompok
1. Muhammad Jovan Adiwijaya Yanuarsyah (5027201025)
2. Muhammad Naufal Pasya (5027201045)
3. Fatchia Farhan (5027201044)

## Deskripsi
Laporan ini dibuat dengan tujuan untuk menjelaskan pengerjaan serta permasalahan yang kami alami dalam pengerjaan soal shift Jaringan Komunikasi modul 3 tahun 2022.

## Gambar Topologi
Berikut merupakan gambar dari topologi yang sudah kami buat

![image](https://user-images.githubusercontent.com/90241858/201462506-b8256338-75d2-4cd1-8adb-f23b77a4e8e1.png)


## Nomor 1
Loid bersama Franky berencana membuat peta tersebut dengan kriteria WISE sebagai DNS Server, Westalis sebagai DHCP Server, Berlint sebagai Proxy Server

### Jawaban Nomor 1

WISE -> DNS Server
Untuk membuat WISE menjadi DNS server kami menginstall bind terlebih dahulu pada WISE dengan script berikut:

```
apt-get update
apt-get install bind9 -y

echo "
zone \"ita03.com\" {
	type master;
	file \"/etc/bind/ita/ita03.com\";
};
">/etc/bind/named.conf.local
```

Setelah itu, kami juga membuat sebuah direktori baru dengan nama `ita` dengan command `mkdir -p /etc/bind/ita` dan meng-copy isi dari `/etc/bind/db.local` ke `/etc/bind/ita/ita03.com` menggunakan command `cp /etc/bind/db.local /etc/bind/ita/ita03.com`. Setelah itu, kami juga mengubah konfigurasi dari `bind` menggunakan script berikut:

```
echo "
;
; BIND data file for local loopback interface
;
\$TTL    604800
@       IN      SOA     localhost. root.localhost. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      ita03.com.
@       IN      A       10.41.2.2       ;
@       IN      AAAA    ::1
">/etc/bind/ita/ita03.com
```
### Testing

![image](https://user-images.githubusercontent.com/90241858/201464767-52acd923-692d-4174-bc41-fe549b0ba838.png)


Westalis -> DHCP Server
Untuk membuat Westalis menjadi DHCP Server kami menginstall `isc-dhcp-server` terlebih dahulu dengan script berikut
```
apt-get update
apt-get install isc-dhcp-server -y
```
Setelah itu, karena Westalis terhubung ke Ostania melalui eth0 maka kami ubah isi dari file '/etc/default/isc-dhcp-server' menjadi sebagai berikut:
```
# Defaults for isc-dhcp-server initscript
# sourced by /etc/init.d/isc-dhcp-server
# installed at /etc/default/isc-dhcp-server by the maintainer scripts

#
# This is a POSIX shell fragment
#

# Path to dhcpd's config file (default: /etc/dhcp/dhcpd.conf).
#DHCPD_CONF=/etc/dhcp/dhcpd.conf

# Path to dhcpd's PID file (default: /var/run/dhcpd.pid).
#DHCPD_PID=/var/run/dhcpd.pid

# Additional options to start dhcpd with.
#       Don't use options -cf or -pf here; use DHCPD_CONF/ DHCPD_PID instead
#OPTIONS=""

# On what interfaces should the DHCP server (dhcpd) serve DHCP requests?
#       Separate multiple interfaces with spaces, e.g. "eth0 eth1".
INTERFACES=\"eth0\"
">/etc/default/isc-dhcp-server
```

### Testing

![image](https://user-images.githubusercontent.com/90241858/201464783-49cda06d-df18-412a-a9e2-2e30cfc117a8.png)


Berlint -> Proxy Server
Untuk mengubah Berlint sebagai proxy server kami melakukan instalasi squid terlbeih dahulu dengan bantuan script berikut:
```
apt-get update

apt-get install squid -y

mv /etc/squid/squid.conf /etc/squid/squid.conf.bak

echo "
include /etc/squid/acl.conf

http_port 8080
http_access allow AVAILABLE_WORKING
http_access deny all
visible_hostname Berlint
">/etc/squid/squid.conf
```


## Nomor 2
Ostania sebagai DHCP Relay

### Jawaban Nomor 2
Pada Ostania kita menjalankan command sebagai berikut:
```
apt-get install isc-dhcp-server
```
Interface yang kami gunakan adalah `eth1 eth2 eth3`
```
echo "
INTERFACES=/"eth1 eth2 eth3/"
">/etc/default/isc-dhcp-relay

```
dan tampilannya seperti gambar berikut:

![dhcp relay](https://user-images.githubusercontent.com/90241942/201454507-7c4c140a-be6b-46f1-bac9-edcce4aeb215.png)


## Nomor 3
Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.50 - [prefix IP].1.88 dan [prefix IP].1.120 - [prefix IP].1.155

### Jawaban Nomor 3
Pertama, kami lakukan konfigurasi pada DHCP Relay pada Ostania  di dalam file `/etc/default/isc-dhcp-relay` sehingga menjadi:
```
# Defaults for isc-dhcp-relay initscript
# sourced by /etc/init.d/isc-dhcp-relay
# installed at /etc/default/isc-dhcp-relay by the maintainer scripts

#
# This is a POSIX shell fragment
#

# What servers should the DHCP relay forward requests to?
SERVERS=/"10.41.2.4/"

# On what interfaces should the DHCP relay (dhrelay) serve DHCP requests?
INTERFACES=/"eth1 eth2 eth3/"

# Additional options that are passed to the DHCP relay daemon?
OPTIONS=/"/"
```

Setelah itu, pada DHCP Server pada Westalis kami menambahkan konfigurasi untuk rentang IP yang akan diberikan pada file `/etc/dhcp/dhcpd.conf` dengan cara:
```
subnet 10.41.2.0 netmask 255.255.255.0 {}

subnet 10.41.1.0 netmask 255.255.255.0 {
    range 10.41.1.50 10.41.1.88;
    range 10.41.1.120 10.41.1.155;
    option routers 10.41.1.1;
    option broadcast-address 10.41.1.255;
    option domain-name-servers 10.41.2.2;
    default-lease-time 300;
    max-lease-time 6900;
}
```

## Nomor 4
Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.10 - [prefix IP].3.30 dan [prefix IP].3.60 - [prefix IP].3.85

### Jawaban Nomor 4
Lakukan konfigurasi pada DHCP Server pada Westalis untuk rentang IP yang akan diberikan pada file `/etc/dhcp/dhcpd.conf` dengan cara menambahkan konfigurasi berikut ini:
```
subnet 10.41.3.0 netmask 255.255.255.0 {
    range 10.41.3.10 10.41.3.30;
    range 10.41.3.60 10.41.3.85;
    option routers 10.41.3.1;
    option broadcast-address 10.41.3.255;
    option domain-name-servers 10.41.2.2;
    default-lease-time 600;
    max-lease-time 6900;
}
```


## Nomor 5
Client mendapatkan DNS dari WISE dan client dapat terhubung dengan internet melalui DNS tersebut

### Jawaban Nomor 5
Supaya client mendapatkan DNS dari WISE diperlukan konfigurasi pada file `/etc/dhcp/dhcpd.conf` dengan `option domain-name-servers 10.45.2.2`;

Supaya semua client dapat terhubung internet pada WISE diberikan konfigurasi pada file `/etc/bind/named.conf.options` seperti berikut:
```
options {
        directory \"/var/cache/bind\";

         forwarders {
                192.168.122.1;
         };

        allow-query{any;};

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};
```


## Nomor 6
Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 5 menit sedangkan pada client yang melalui Switch3 selama 10 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 115 menit

### Jawaban Nomor 6
Pada subnet interface switch 1 dan 3 ditambahkan konfigurasi berikut pada file `/etc/dhcp/dhcpd.conf`:
```
subnet 10.41.1.0 netmask 255.255.255.0 {
    default-lease-time 300;
    max-lease-time 6900;
}

subnet 10.41.3.0 netmask 255.255.255.0 {
    default-lease-time 600;
    max-lease-time 6900;
}
```

## Nomor 7
Loid dan Franky berencana menjadikan Eden sebagai server untuk pertukaran informasi dengan alamat IP yang tetap dengan IP [prefix IP].3.13

### Jawaban Nomor 7
Pertama-tama kami menambahkan konfigurasi untuk fixed address pada `/etc/dhcp/dhcpd.conf` seperti berikut:
```
host Eden {
    hardware ethernet 32:f8:34:9e:88:3b;
    fixed-address 10.41.3.13;
}
```

Setelah itu kami juga menambahkan konfigurasi pada file `/etc/network/interfaces` di Eden dengan:
```
hwaddress ether 32:f8:34:9e:88:3b
```
Maka Eden akan mendapatkan ip `10.41.3.13`

## Nomor 8
SSS, Garden, dan Eden digunakan sebagai client Proxy agar pertukaran informasi dapat terjamin keamanannya, juga untuk mencegah kebocoran data.

Pada Proxy Server di Berlint, Loid berencana untuk mengatur bagaimana Client dapat mengakses internet. Artinya setiap client harus menggunakan Berlint sebagai HTTP & HTTPS proxy. Adapun kriteria pengaturannya adalah sebagai berikut:

1. Client hanya dapat mengakses internet diluar (selain) hari & jam kerja (senin-jumat 08.00 - 17.00) dan hari libur (dapat mengakses 24 jam penuh)
2. Adapun pada hari dan jam kerja sesuai nomor (1), client hanya dapat mengakses domain loid-work.com dan franky-work.com (IP tujuan domain dibebaskan)
3. Saat akses internet dibuka, client dilarang untuk mengakses web tanpa HTTPS. (Contoh web HTTP: http://example.com)
4. Agar menghemat penggunaan, akses internet dibatasi dengan kecepatan maksimum 128 Kbps pada setiap host (Kbps = kilobit per second; lakukan pengecekan pada tiap host, ketika 2 host akses internet pada saat bersamaan, keduanya mendapatkan speed maksimal yaitu 128 Kbps)
5. Setelah diterapkan, ternyata peraturan nomor (4) mengganggu produktifitas saat hari kerja, dengan demikian pembatasan kecepatan hanya diberlakukan untuk pengaksesan internet pada hari libur

Setelah proxy Berlint diatur oleh Loid, dia melakukan pengujian dan mendapatkan hasil sesuai tabel berikut.

![image](https://user-images.githubusercontent.com/90241858/201464985-e4d1dcca-fd1a-4df0-9437-4444c2e96afc.png)

### Jawaban Nomor 8
Untuk nomor 8 kami hanya dapat mengerjakan poin 1 yaitu untuk Client hanya dapat mengakses internet diluar (selain) hari & jam kerja (senin-jumat 08.00 - 17.00) dan hari libur (dapat mengakses 24 jam penuh) dengan cara mengubah konfigurasi pada file `/etc/squid/acl.conf` menjadi:
```
acl AVAILABLE_WORKING time MTWHF 08:00-17:00
```
