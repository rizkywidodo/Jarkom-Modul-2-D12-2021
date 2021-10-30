# Jarkom-Modul-2-D12-2021
Laporan Resmi Praktikum Jaringan Komputer 2021 - D12
- Nur Hidayati (05111940000028)
- Pramudityo Prabowo (05111940000210)
- Muhammad Rizky Widodo (05111940000216)

## Membuat Topologi
Sebelum masuk ke dalam soal, kita harus membuat topologi sesuai dengan yang diminta. Topologinya yaitu sebagai berikut:

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/0_1.png?raw=true)

## Membuat Konfigurasi Network pada Router dan Setiap Node

1. Foosha sebagai router

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/0_2.jpg?raw=true)

2. EniesLobby sebagai DNS Master

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/0_3.jpg?raw=true)

3. Water7 sebagai DNS Slave

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/0_4.jpg?raw=true)

4. Skypie sebagai Web Server

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/0_5.jpg?raw=true)

5. Loguetown sebagai Client

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/0_6.jpg?raw=true)

6. Alabasta sebagai Client

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/0_7.jpg?raw=true)

## SOAL 1
Semua node (EniesLobby, Water7, Skypie, Loguetown, dan Alabasta) terhubung pada router Foosha, sehingga dapat mengakses internet.
### Solusi
Menjalankan command `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.27.0.0/16` agar bisa mengakses jaringan keluar pada router Foosha.

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/1_1.jpg?raw=true)

Agar command dijalankan setiap kali project di start, maka harus menambahkan command tersebut ke `/root/.bashrc` dengan menggunakan command `echo "iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.27.0.0/16" >> /root/.bashrc`

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/1_2.jpg?raw=true)

Selanjutnya, pada masing-masing node menjalankan command `echo nameserver 192.168.122.1 > /etc/resolv.conf` untuk setting IP DNS agar terhubung ke internet. Agar command dijalankan setiap kali project di start, maka harus menambahkan command tersebut ke `/root/.bashrc` dengan menggunakan command `echo "echo nameserver 192.168.122.1 > /etc/resolv.conf" >> /root/.bashrc`

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/1_3.jpg?raw=true)

Untuk memastikan masing-masing node sudah terhubung ke internet, dapat dilakukan dengan menjalankan command `ping google.com`. Apabila berhasil, maka hasilnya sebagai berikut:

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/1_4.jpg?raw=true)

## SOAL 2
Membuat website utama dengan mengakses **franky.yyy.com** dengan alias **www.franky.yyy.com** pada folder kaizoku.
### Solusi
Pada DNS Master (EniesLobby) menjalankan command `apt-get update` dan `apt-get install bind9 -y` yang digunakan untuk menginstal bind9.

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/2_1.jpg?raw=true)

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/2_2.jpg?raw=true)

Lalu, mengedit file /etc/bind/named.conf.local dengan menggunakan command `vi /etc/bind/named.conf.local` dan menambahkan baris berikut:
```
zone "franky.d12.com" {
        type master;
        file "/etc/bind/kaizoku/franky.d12.com";
};
```

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/2_3.jpg?raw=true)

Kemudian membuat folder kaizoku dengan command `mkdir /etc/bind/kaizoku`.

Selanjutnya mengcopy file db.local pada path /etc/bind ke dalam folder kaizoku yang baru saja dibuat dan mengubah namanya menjadi franky.d12.com dengan command `cp /etc/bind/db.local /etc/bind/kaizoku/franky.d12.com`.

Lalu, mengedit file tersebut dengan command `vi /etc/bind/kaizoku/franky.d12.com`. Mengkonfigurasi file menjadi sebagai berikut:

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/2_4.jpg?raw=true)

Menjalankan command `service bind9 restart`.

Pada client Loguetown dan Alabasta mengarahkan nameserver menuju IP EniesLobby dengan mengedit file resolv.conf dengan mengetikkan command `vi /etc/resolv.conf`.

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/2_5.jpg?raw=true)

Untuk mencoba koneksi DNS, melakukan command `ping www.franky.d12.com` pada client Loguetown dan Alabasta. Apabila berhasil, hasilnya sebagai berikut:

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/2_6.jpg?raw=true)

## SOAL 3
Buat subdomain **super.franky.yyy.com** dengan alias **www.super.franky.yyy.com** yang diatur DNS nya di EniesLobby dan mengarah ke Skypie.
### Solusi
Pertama pada EniesLobby, mengedit file /etc/bind/kaizoku/franky.d12.com dengan command `vi /etc/bind/kaizoku/franky.d12.com` dan menambahkan
```
ns1       IN      A       10.27.2.4	; IP Skypie
super   IN      NS      ns1

```

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/3_1.jpg?raw=true)

Kedua, menambahkan zone pada file /etc/bind/named.conf.local dengan command `vi /etc/bind/named.conf.local` dan menambahkan
```
zone "super.franky.d12.com" {
            type master;
            file "/etc/bind/kaizoku/super.franky.d12.com";
};
```

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/3_2.jpg?raw=true)

Selanjutnya mengcopy file db.local pada path /etc/bind/ menjadi super.franky.d12.com dengan command `cp /etc/bind/db.local /etc/bind/kaizoku/super.franky.d12.com`.

Lalu, mengedit file tersebut dengan command `vi /etc/bind/kaizoku/super.franky.d12.com`. Mengkonfigurasi file menjadi sebagai berikut:

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/3_3.jpg?raw=true)

Untuk mengeceknya, melakukan command ping `www.super.franky.d12.com` pada client Loguetown dan Alabasta. Apabila berhasil, hasilnya sebagai berikut:

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/3_4.jpg?raw=true)

## SOAL 4
Buat juga reserve domain untuk domain utama.
### Solusi
Pertama, mengedit file /etc/bind/named.conf.local dengan command `vi /etc/bind/named.conf.local` dan menambahkan zone
```
zone "2.27.10.in-addr.arpa" {
            type master;
            file "/etc/bind/kaizoku/2.27.10.in-addr.arpa";
};
```

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/4_1.jpg?raw=true)

Selanjutnya mengcopy file db.local pada path /etc/bind/ menjadi 2.27.10.in-addr.arpa dengan command `cp /etc/bind/db.local /etc/bind/kaizoku/2.27.10.in-addr.arpa`.
Lalu, mengedit file tersebut dengan command `vi /etc/bind/kaizoku/2.27.10.in-addr.arpa`. Mengkonfigurasi file menjadi sebagai berikut:

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/4_2.jpg?raw=true)

Untuk mengeceknya, melakukan command `host -t PTR 10.27.2.2` pada client Loguetown dan Alabasta. Apabila berhasil, hasilnya sebagai berikut:

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/4_3.jpg?raw=true)

## SOAL 5
Apabila server EniesLobby sebagai DNS Master rusak, maka buat Water7 sebagai DNS Slave untuk domain utama.
### Solusi
Pada EniesLobby, mengedit file /etc/bind.named.conf.local dengan command `vi /etc/bind.named.conf.local`, kemudian mengedit zone franky.d12.com menjadi
```
zone "franky.d12.com" {
            type master;
            notify yes;
            also-notify { 10.27.2.3; };	// IP Water7
            allow-transfer { 10.27.2.3; };	// IP Water7
            file "/etc/bind/kaizoku/franky.d12.com";
};
```

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/5_1.jpg?raw=true)

Menjalankan command ```service bind9 restart```.

Lalu, pada Water7 menjalankan command `apt-get update` dan `apt-get install bind9 -y` untuk menginstal bind9.

Mengedit file /etc/bind/named.conf.local dengan command `vi /etc/bind/named.conf.local` dan menambahkan zone berikut:
```
zone "franky.d12.com" {
        type slave;
        masters { 10.27.2.2; };	// IP EniesLobby
        file "/var/lib/bind/franky.d12.com";
};
```

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/5_2.jpg?raw=true)

Melakukan testing dengan mematikan server DNS Master (EniesLobby) dengan command `service bind9 stop` pada EniesLobby.

Pada client Loguetown dan Alabasta menambahkan nameserver Water7 dengan command `vi /etc/resolv.conf`

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/5_3.jpg?raw=true)

Untuk mengeceknya, melakukan command `ping www.franky.d12.com` pada client Loguetown dan Alabasta. Apabila berhasil, hasilnya sebagai berikut:

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/5_4.jpg?raw=true)

## SOAL 6
Terdapat subdomain **mecha.franky.yyy.com** dengan alias **www.mecha.franky.yyy.com** yang didelegasikan dari EniesLobby ke Water7 dengan IP menuju ke Skypie dalam folder sunnygo.
### Solusi
Pada EniesLobby, mengedit file /etc/bind/kaizoku/franky.d12.com dengan command `vi /etc/bind/kaizoku/franky.d12.com`, lalu menambahkan
```
ns2     	IN      A       10.27.2.3 ; IP Water7
mecha   	IN      NS      ns2
```

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/6_1.jpg?raw=true)

Menjalankan command `service bind9 restart`.

Mengedit file /etc/bind/named.conf.options pada EniesLobby kemudian comment **dnssec-validation auto;** dan menambahkan **allow-query{any;};**

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/6_2.jpg?raw=true)

Memastikan sudah ada line allow-transfer { 10.27.2.3; }; pada zone franky.d12.com pada file /etc/bind/named.conf.local.

Pada Water7, mengedit file /etc/bind/named.conf.options kemudian comment **dnssec-validation auto;** dan menambahkan **allow-query{any;};**.

Langkah selanjutnya yaitu mengedit file dengan command `vi /etc/bind/named.conf.local` dan menambahkan zone
```
zone "mecha.franky.d12.com" {
            type master;
            file "/etc/bind/sunnygo/mecha.franky.d12.com";
};
```

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/6_3.jpg?raw=true)

Kemudian membuat folder sunnygo dengan command `mkdir /etc/bind/sunnygo`. 
Selanjutnya mengcopy file db.local pada path /etc/bind ke dalam folder sunnygo yang baru saja dibuat dan mengubah namanya menjadi mecha.franky.d12.com dengan command `cp /etc/bind/db.local /etc/bind/sunnygo/mecha.franky.d12.com`.
Lalu, mengedit file tersebut dengan command `vi /etc/bind/sunnygo/mecha.franky.d12.com`. Mengkonfigurasi file menjadi sebagai berikut:

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/6_4.jpg?raw=true)

Menjalankan command `service bind9 restart`.

Menambahkan nameserver Skypie pada client Loguetown dengan command `vi /etc/resolv.conf`.

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/6_5.jpg?raw=true)

Untuk mengeceknya, melakukan command `ping www.mecha.franky.d12.com` pada client Loguetown dan Alabasta. Apabila berhasil, hasilnya sebagai berikut:

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/6_6.jpg?raw=true)

## SOAL 7
Buat subdomain melalui Water7 dengan nama **general.mecha.franky.yyy.com** dengan alias **www.general.mecha.franky.yyy.com** yang mengarah ke Skypie.
### Solusi
Pada Water7, mengedit file /etc/bind/sunnygo/mecha.franky.d12.com dengan command `vi /etc/bind/sunnygo/mecha.franky.d12.com` dan menambahkan
```
ns1     	IN      A       10.27.2.4 ; IP Skypie
general 	IN      NS      ns1
```

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/7_1.jpg?raw=true)

Selanjutnya mengedit file /etc/bind/names.conf.local dengan command `vi etc/bind/named.conf.local` dan menambahkan zone
```
zone "general.mecha.franky.d12.com" {
            type master;
            file "/etc/bind/sunnygo/general.mecha.franky.d12.com";
};
```

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/7_2.jpg?raw=true)

Selanjutnya mengcopy file db.local pada path /etc/bind menjadi general.mecha.franky.d12.com dengan command `cp /etc/bind/db.local /etc/bind/sunnygo/general.mecha.franky.d12.com`.

Lalu, mengedit file tersebut dengan command `vi /etc/bind/sunnygo/general.mecha.franky.d12.com`. Mengkonfigurasi file menjadi sebagai berikut:

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/7_3.jpg?raw=true)

Menjalankan command `service bind9 restart`.

Untuk mengeceknya, melakukan command `ping www.general.mecha.franky.d12.com` pada client Loguetown dan Alabasta. Apabila berhasil, hasilnya sebagai berikut:

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/7_4.jpg?raw=true)

## SOAL 8
Setelah melakukan konfigurasi server, maka dilakukan konfigurasi Webserver. Pertama dengan webserver **www.franky.yyy.com**. Buat webserver dengan DocumentRoot pada /var/www/franky.yyy.com.
### Solusi:
Pada EniesLobby, memastikan bahwa IP mengarah ke web server (Skypie) pada  `vi /etc/bind/kaizoku/franky.d12.com` dengan konfigurasi 

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/8_0.jpg?raw=true)

Pada Skypie menginstal apache2, php, libapache2-mod-php7.0. Untuk command instalasinya dapat disimpan di dalam script.sh.

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/8_1.jpg?raw=true)

Langkah selanjutnya yaitu mendownload file yang diperlukan dengan command `wget` selanjutnya file di-extract menggunakan command `unzip`

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/8_2.jpg?raw=true)

Lalu berpindah ke direktori /etc/apache2/sites-available kemudian mengcopy file 000-default.conf menjadi franky.d12.com.conf dengan command `cp file 000-default.conf franky.d12.com.conf`

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/8_3.jpg?raw=true)

Mengedit file franky.d12.com.conf dengan command `vi franky.d12.com.conf` dan mengubahnya menjadi
```
ServerAdmin webmaster@localhost
DocumentRoot /var/www/franky.d12.com
Servername franky.d12.com
ServerAlias www.franky.d12.com
```

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/8_4.jpg?raw=true)

Kemudian membuat directory baru dengan nama franky.d12.com pada /var/www/ menggunakan command `mkdir /var/www/franky.d12.com.` Lalu mengcopy isi dari folder franky yang telah didownload ke /var/www/franky.d12.com dengan command` cp -r /root/Praktikum-Modul-2-Jarkom-main/franky/* /var/www/franky.d12.com`

Setelah itu menjalankan `command a2ensite franky.d1.com` dan `service apache2 restart`

Untuk mengeceknya pada Loguetown menggunakan command `lynx franky.d12.com.` Bila berhasil maka hasilnya 

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/8_5.jpg?raw=true)

## SOAL 9
Setelah itu, Luffy juga membutuhkan agar url **www.franky.yyy.com/index.php/home** dapat menjadi menjadi **www.franky.yyy.com/home**. 
### Solusi:
Pada Skypie /etc/apache2/sites-available, menjalankan perintah `a2enmod rewrite` kemudian `service apache2 restart`.

Kemudian berpindah ke directory /var/www/franky.d12.com dan membuat file .htaccess dengan command `vi .htaccess` dengan isi file:
```
RewriteEngine On
RewriteRule ^home$ index.php/home
```
![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/9_1.jpg?raw=true)

Kemudian membuka file /etc/apache2/sites-available/franky.d12.com.conf dan menambahkan:
```
<Directory /var/www/franky.d12.com>
        Options +FollowSymLinks -Multiviews
        AllowOverride All
</Directory>
```
![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/9_2.jpg?raw=true)

Menjalankan command `service apache2 restart`

Untuk mengeceknya pada Loguetown menggunakan command `lynx franky.d12.com/home` , bila berhasil maka hasilnya 

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/9_3.jpg?raw=true)

## SOAL 10
Pada subdomain **www.super.franky.yyy.com**, Luffy membutuhkan penyimpanan aset yang memiliki DocumentRoot pada /var/www/super.franky.yyy.com.
### Solusi:
Pertama, memindahkan ke directory /etc/apache2/sites-available. Kemudian mencopy file 000-default.conf menjadi file super.franky.d12.com.conf dengan command `cp 000-default.conf super.franky.d12.com.conf`. Lalu mengedit file super.franky.d12.com.conf agar menjadi sebagai berikut
```
ServerAdmin webmaster@localhost
DocumentRoot /var/www/super.franky.d12.com
ServerName super.franky.d12.com
ServerAlias www.super.franky.d12.com
```

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/10_1.jpg?raw=true)

Kemudian membuat directory baru dengan nama super.franky.d12.com pada /var/www/ menggunakan command mkdir /var/www/super.franky.d12.com. Lalu mengcopy isi dari folder super.franky yang telah didownload ke /var/www/super.franky.d12.com dengan command `cp -r /root/Praktikum-Modul-2-Jarkom-main/super.franky/* /var/www/super.franky.d12.com.`

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/10_2.jpg?raw=true)

Setelah itu menjalankan command `a2ensite super.franky.d12.com` dan `service apache2 restart`

Untuk mengeceknya pada Loguetown menggunakan command `lynx super.franky.d12.com` , bila berhasil maka hasilnya 

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/10_3.jpg?raw=true)

## SOAL 11
Akan tetapi, pada folder /public, Luffy ingin hanya dapat melakukan directory listing saja.
### Solusi:

Pada Skypie, berpindah ke direktori /etc/apache2/sites-available dan mengedit file super.franky.d12.com.conf menjadi

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/11_1.jpg?raw=true)

Menjalankan command `service apache2 restart`

Untuk mengeceknya pada Loguetown menggunakan command `lynx super.franky.d12.com/public` , bila berhasil maka hasilnya 

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/11_2.jpg?raw=true)

Karena soal ini hanya meminta untuk melakukan directory listing pada folder `/public`, maka akses forbidden apabila mengakses sub foldernya.

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/11_3.jpg?raw=true)

## SOAL 12
Tidak hanya itu, Luffy juga menyiapkan error file 404.html pada folder /error untuk mengganti error kode pada apache
### Solusi:

Pada Skypie, berpindah ke direktori /etc/apache2/sites-available dan mengedit file super.franky.d12.com.conf menjadi
```
ErrorDocument 404 /error/404.html
```

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/12_1.jpg?raw=true)

Menjalankan command `service apache2 restart`

Untuk mengeceknya pada Loguetown menggunakan command `lynx super.franky.d12.com/halo` , bila berhasil maka hasilnya 

![alt text](https://github.com/rizkywidodo/Jarkom-Modul-2-D12-2021/blob/main/images/12_2.jpg?raw=true)

## SOAL 13

## SOAL 14

## SOAL 15

## SOAL 16

## SOAL 17

### Kendala Selama Pengerjaan
Adapun beberapa kendala yang kami alami selama pengerjaan soal yaitu sebagai berikut:
1. Ketika lupa meletakkan command pada root atau script, saat project di close dan akan di start kembali ada beberapa konfigurasi yang hilang yang bisa menyebabkan error dan harus mengetik ulang.
2. Mengalami kendala saat menggunakan regex sesuai dengan yang dibutuhkan.
3. Mengalami beberapa kesalahan penulisan command.
