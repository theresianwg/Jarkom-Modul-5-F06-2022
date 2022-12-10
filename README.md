# Jarkom-Modul-5-F06-2022

## Kelompok F06

|               Nama               |      NRP      |
| -------------------------------- | ------------- |
| Benedictus Bimo Cahyo Wicaksono  |  5025201097   |  
| Andhika Ditya Bagaskara D.       |  5025201096   |
| Theresia Nawangsih               |  5025201144   |

# (A) Topologi Soal
<p align="center">
  <img src="img/topologi.png" width="600">
</p><br>

## Keterangan :	
- Eden adalah DNS Server
- WISE adalah DHCP Server
- Garden dan SSS adalah Web Server
- Jumlah Host pada Forger adalah 62 host
- Jumlah Host pada Desmond adalah 700 host
- Jumlah Host pada Blackbell adalah 255 host
- Jumlah Host pada Briar adalah 200 host

### Eden:
```
apt-get update
apt-get install bind9 -y
```

### WISE:
```
apt-get update
apt-get install isc-dhcp-server -y
```

### Garden & SSS:
```
apt-get update
apt-get install apache2
```

# (B) Subnetting
## Untuk menjaga perdamaian dunia, Loid ingin meminta kalian untuk membuat topologi tersebut menggunakan teknik CIDR atau VLSM setelah melakukan subnetting.

### Jawab
Pada modul 5 ini, kami menggunakan perhitungan dengan metode VLSM
<p align="center">
  <img src="img/label.png" width="600">
</p><br>

Pada pembagian ini, terdapat 8 subnet dimulai dari A1-A8. Selanjutnya, kita dapat melakukan pembagian seperti tabel dibawah ini
<p align="center">
  <img src="img/penentuan.png" width="600">
</p><br>

Selanjutnya, mengitung pembagian IP dengan membuat tree.
<p align="center">
  <img src="img/pohon.png" width="600">
</p><br>

Hasil pembagian IP
<p align="center">
  <img src="img/perhitungan.png" width="600">
</p><br>

### Konfigurasi
- Strix 
```
auto eth0
iface eth0 inet static
        address 192.168.122.2
        netmask 255.255.255.252
        gateway 192.168.122.1

auto eth1
iface eth1 inet static
	address 192.202.0.1
	netmask 255.255.255.252

auto eth2
iface eth2 inet static
	address 192.202.0.5
	netmask 255.255.255.252
```

- Westalis
```
auto eth0
iface eth0 inet static
	address 192.202.0.2
	netmask 255.255.255.252
        gateway 192.202.0.1

auto eth1
iface eth1 inet static
	address 192.202.4.1
	netmask 255.255.252.0

auto eth2
iface eth2 inet static
	address 192.202.0.129
	netmask 255.255.255.128

auto eth3
iface eth3 inet static
	address 192.202.0.9
	netmask 255.255.255.248
```

- Ostania
```
auto eth0
iface eth0 inet static
	address 192.202.0.6
	netmask 255.255.255.252
        gateway 192.202.0.5

auto eth1
iface eth1 inet static
	address 192.202.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.202.2.1
	netmask 255.255.254.0

auto eth3
iface eth3 inet static
	address 192.202.0.17
	netmask 255.255.255.248
```
- Eden
```
auto eth0
iface eth0 inet static
	address 192.202.0.10
	netmask 255.255.255.248
	gateway 192.202.0.9
```

- Wise 
```
auto eth0
iface eth0 inet static
	address 192.202.0.11
	netmask 255.255.255.248
	gateway 192.202.0.9
```

- Garden
```
auto eth0
iface eth0 inet static
	address 192.202.0.18
	netmask 255.255.255.248
	gateway 192.202.0.17
```

- SSS
```
auto eth0
iface eth0 inet static
	address 192.202.0.19
	netmask 255.255.255.248
	gateway 192.202.0.17
```

- Blackbell, Briar, Desmond, Forger
```
auto eth0
iface eth0 inet dhcp
```

# (C) ROUTING
## Anya, putri pertama Loid, juga berpesan kepada anda agar melakukan Routing agar setiap perangkat pada jaringan tersebut dapat terhubung.

### Jawab
- Strix
```
#A1 to Westalis
route add -net 192.202.0.8 netmask 255.255.255.248 gw 191.202.0.2
#A2 to Westalis 
route add -net 192.202.0.128 netmask 255.255.255.128 gw 192.202.0.2
#A3 to Westalis
route add -net 192.202.4.0 netmask 255.255.252.0 gw 192.202.0.2
#A8 to Ostania
route add -net 192.202.0.16 netmask 255.255.255.248 gw 192.202.0.6
#A7 to Ostania
route add -net 192.202.2.0 netmask 255.255.254.0 gw 192.202.0.6
#A6 to Ostania
route add -net 192.202.1.0 netmask 255.255.255.0 gw 192.202.0.6
```

# (D) SETTING RELAY & DHCP SERVER
## 
Tugas berikutnya adalah memberikan ip pada subnet Forger, Desmond, Blackbell, dan Briar secara dinamis menggunakan bantuan DHCP server. Kemudian kalian ingat bahwa kalian harus setting DHCP Relay pada router yang menghubungkannya.

### Jawab
### DHCP RELAY
Pada setiap Router (Strix, Ostania, Westalis), buka ``/etc/default/isc-dhcp-relay``, tambahkan konfigurasi seperti dibawah ini:

```
# What servers should the DHCP relay forward requests to?
SERVERS="192.202.0.11" # IP WISE

# On what interfaces should the DHCP relay (dhrelay) serve DHCP requests?
INTERFACES="eth0 eth1 eth2 eth3"

# Additional options that are passed to the DHCP relay daemon?
OPTIONS=""
```

### DHCP SERVER
Pada Wise akan ditambahkan konfigurasi seperti dibawah ini
```
subnet 192.202.0.128 netmask 255.255.255.128 {
    range 192.202.0.130 192.202.0.255;
    option routers 192.202.0.129;
    option broadcast-address 192.202.0.255;
    option domain-name-servers 192.202.0.10;
    default-lease-time 360;
    max-lease-time 7200;
}

subnet 192.202.0.8 netmask 255.255.255.248 {
}

subnet 192.202.4.0 netmask 255.255.252.0 {
    range 192.202.4.2 192.202.7.255;
    option routers 192.202.4.1;
    option broadcast-address 192.202.7.255;
    option domain-name-servers 192.202.0.10;
    default-lease-time 360;
    max-lease-time 7200;
}
subnet 192.202.2.0 netmask 255.255.254.0 {
    range 192.202.2.2 192.202.3.255;
    option routers 192.202.2.1;
    option broadcast-address 192.202.3.255;
    option domain-name-servers 192.202.0.10;
    default-lease-time 360;
    max-lease-time 7200;
}
subnet 192.202.1.0 netmask 255.255.255.0 {
    range 192.202.1.2 192.202.1.255;
    option routers 192.202.1.1;
    option broadcast-address 192.202.1.255;
    option domain-name-servers 192.202.0.10;
    default-lease-time 360;
    max-lease-time 7200;
}
```

# SOAL 1
## Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Strix menggunakan iptables, tetapi Loid tidak ingin menggunakan MASQUERADE.

### Jawab :
Pada soal ini diminta untuk tidak menggunakan MASQUERADE, maka dari itu kita dapat menggunakan ```SNAT``` pada STRIX seperti dibawah ini

``` iptables -t nat -A POSTROUTING -o eth0 -j SNAT -s 192.202.0.0/21 --to-source 192.168.122.2 ```

testing: lakukan ping google.com pada setiap node, atau apt-get update & install pada node selain client

# SOAL 2
## Kalian diminta untuk melakukan drop semua TCP dan UDP dari luar Topologi kalian pada server yang merupakan DHCP Server demi menjaga keamanan.

### Jawab :
Karena Wise merupakan DHCP Server dan semua pada TCP dan UDP di drop. kita dapat menambahkan syntax iptables pada WISE seperti dibawah ini

```
iptables -A INPUT -p tcp -j REJECT
iptables -A INPUT -p udp -j REJECT
```

testing: lakukan apt-get update, apabila terjadi error, maka drop packet berhasil

# SOAL 3
## Loid meminta kalian untuk membatasi DHCP dan DNS Server hanya boleh menerima maksimal 2 koneksi ICMP secara bersamaan menggunakan iptables, selebihnya didrop.

### Jawab :
Pada soal ini limit maksimal untuk mengakses koneksi secara bersamaan ialah 2, maka dari itu kita dapat menggunakan `--connlimit-above 2` dan agar selain 2 koneksi ditolak dapat menggunakan `DROP`, maka tambahkan syntax seperti dibawah ini pada WISE (DHCP Server) dan Eden (DNS Server):

``` iptables -A INPUT -p icmp -m connlimit --connlimit-above 2 --connlimit-mask 0 -j DROP ```

testing: lakukan ping DNS Server (192.202.0.10) atau DHCP Server (192.202.0.11) pada tiap client, apabila 2 client terakhir tidak mendapatkan response, maka limit berhasil

# SOAL 4
## Akses menuju Web Server hanya diperbolehkan disaat jam kerja yaitu Senin sampai Jumat pada pukul 07.00 - 16.00.

### Jawab :
Karena akses menuju web server hanya pada saat jam kerja, maka dari itu kita dapat menggunakan `--timestart 07:00` untuk memulai dan `--timestop 16:00` untuk mengakhiri. Dan juga kita dapat membatasi hari dengan menggunakan `--weekdays`. Untuk menentukan bisa tidak nya web server diakses menggunakan `ACCEPT` dan `REJECT`pada Web Server (SSS dan Garden), seperti dibawah ini

```
iptables -A INPUT -m time --timestart 07:00 --timestop 16:00 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT
iptables -A INPUT -j REJECT
```

testing: ping IP Web Server (192.202.0.18 atau 192.202.0.19) pada hari dan jam ACCEPT dan oada hari dan jam REJECT

# SOAL 5
## Karena kita memiliki 2 Web Server, Loid ingin Ostania diatur sehingga setiap request dari client yang mengakses Garden dengan port 80 akan didistribusikan secara bergantian pada SSS dan Garden secara berurutan dan request dari client yang mengakses SSS dengan port 443 akan didistribusikan secara bergantian pada Garden dan SSS secara berurutan.

### Jawab :
Pada soal ini diminta untuk mendistribusikan secara bergantian pada SSS dan Garden, maka dari itu disini kita dapat menggunakan `--dport`. Dan juga, dapat membatasi dengan menggunakan `--every 2` dan `--to-destination` mengarahkan distribusinya ke node lain
```
iptables -A PREROUTING -t nat -p tcp --dport 80 -d 192.202.0.18 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.202.0.18:80
iptables -A PREROUTING -t nat -p tcp --dport 80 -d 192.202.0.18 -j DNAT --to-destination 192.202.0.19:80
iptables -A PREROUTING -t nat -p tcp --dport 443 -d 192.202.0.19 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.202.0.19:443
iptables -A PREROUTING -t nat -p tcp --dport 443 -d 192.202.0.19 -j DNAT --to-destination 192.202.0.18:443
```


# SOAL 6
## Karena Loid ingin tau paket apa saja yang di-drop, maka di setiap node server dan router ditambahkan logging paket yang di-drop dengan standard syslog level.

### Jawab :

## Kendala :
- Soal no 5 tidak dapat menyambungkan curl saat testing











