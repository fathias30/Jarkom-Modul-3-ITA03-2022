# Jarkom-Modul-3-ITA03-2022

## Anggota Kelompok
1. Muhammad Jovan Adiwijaya Yanuarsyah (5027201025)
2. Muhammad Naufal Pasya (5027201045)
3. Fatchia Farhan (5027201044)

## Deskripsi
Laporan ini dibuat dengan tujuan untuk

## Gambar Topologi

## Nomor 1
Loid bersama Franky berencana membuat peta tersebut dengan kriteria WISE sebagai DNS Server, Westalis sebagai DHCP Server, Berlint sebagai Proxy Server

### Jawaban Nomor 1

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

## Nomor 4
Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.10 - [prefix IP].3.30 dan [prefix IP].3.60 - [prefix IP].3.85

### Jawaban Nomor 4

## Nomor 5
Client mendapatkan DNS dari WISE dan client dapat terhubung dengan internet melalui DNS tersebut

### Jawaban Nomor 5

## Nomor 6
Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 5 menit sedangkan pada client yang melalui Switch3 selama 10 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 115 menit

### Jawaban Nomor 6

## Nomor 7
Loid dan Franky berencana menjadikan Eden sebagai server untuk pertukaran informasi dengan alamat IP yang tetap dengan IP [prefix IP].3.13

### Jawaban Nomor 7

## Nomor 8

### Jawaban Nomor 8
