# Jarkom-Modul-3-IT22-2024

|Nama  | NRP |
|--|--|
| Jacinta Syilloam | 5027221036 |

# Topology
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/b3af69f4-4f62-410f-9457-7e0eb9373e13)

# Network Configuration

| **Node**       | **Interface** | **IP Address**  | **Netmask**         | **Gateway**       | **Configuration** |
|----------------|---------------|-----------------|---------------------|-------------------|-------------------|
| Arakis (Router/DHCP Relay) | eth0 | DHCP            | -                   | -                 | DHCP              |
| Arakis (Router/DHCP Relay) | eth1 | 192.244.1.1     | 255.255.255.0       | -                 | Static            |
| Arakis (Router/DHCP Relay) | eth2 | 192.244.2.1     | 255.255.255.0       | -                 | Static            |
| Arakis (Router/DHCP Relay) | eth3 | 192.244.3.1     | 255.255.255.0       | -                 | Static            |
| Arakis (Router/DHCP Relay) | eth4 | 192.244.4.1     | 255.255.255.0       | -                 | Static            |
| Mohiam (DHCP Server)       | eth0 | 192.244.3.2     | 255.255.255.0       | 192.244.3.1       | Static            |
| Irulan (DNS Server)        | eth0 | 192.244.3.3     | 255.255.255.0       | 192.244.3.1       | Static            |
| Chani (Database Server)    | eth0 | 192.244.4.2     | 255.255.255.0       | 192.244.4.1       | Static            |
| Stilgar (Load Balancer)    | eth0 | 192.244.4.3     | 255.255.255.0       | 192.244.4.1       | Static            |
| Leto (Laravel Worker)      | eth0 | 192.244.2.2     | 255.255.255.0       | 192.244.2.1       | Static            |
| Duncan (Laravel Worker)    | eth0 | 192.244.2.3     | 255.255.255.0       | 192.244.2.1       | Static            |
| Jessica (Laravel Worker)   | eth0 | 192.244.2.4     | 255.255.255.0       | 192.244.2.1       | Static            |
| Vladimir (PHP Worker)      | eth0 | 192.244.1.2     | 255.255.255.0       | 192.244.1.1       | Static            |
| Rabban (PHP Worker)        | eth0 | 192.244.1.3     | 255.255.255.0       | 192.244.1.1       | Static            |
| Feyd (PHP Worker)          | eth0 | 192.244.1.4     | 255.255.255.0       | 192.244.1.1       | Static            |
| Dmitri (Client)            | eth0 | DHCP            | -                   | -                 | DHCP              |
| Paul (Client)              | eth0 | DHCP            | -                   | -                 | DHCP              |


# [ 0 ]
> Planet Caladan sedang mengalami krisis karena kehabisan spice, klan atreides berencana untuk melakukan eksplorasi ke planet arakis dipimpin oleh duke leto mereka meregister domain name atreides.yyy.com untuk worker Laravel mengarah pada Leto Atreides. Namun ternyata tidak hanya klan atreides yang berusaha melakukan eksplorasi, Klan harkonen sudah mendaftarkan domain name harkonen.yyy.com untuk worker PHP (0) mengarah pada Vladimir Harkonen

## @ Irulan (DNS Server)

Enable internet access
```
echo 'nameserver 192.168.122.1' > /etc/resolv.conf
```

<br />

Install dependencies
```
apt-get update -y
apt-get install bind9 -y
```

<br />

Write the domain configuration in ```/etc/bind/named.conf.local```

```
echo '
zone "atreides.it22.com" {
	type master;
	file "/etc/bind/it22/atreides.it22.com";
};' > /etc/bind/named.conf.local

echo '
zone "harkonen.it22.com" {
	type master;
	file "/etc/bind/it22/harkonen.it22.com";
};' >> /etc/bind/named.conf.local
```

<br />

Create /etc/bind/it22 directory
```
mkdir -p /etc/bind/it22
```

<br />

Edit the DNS record
```
echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     atreides.it22.com. root.atreides.it22.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      atreides.it22.com.
@       IN      A       192.244.2.2
@       IN      AAAA    ::1' > /etc/bind/it22/atreides.it22.com

echo "DNS configuration for atreides.it22.com has been set up."

echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     harkonen.it22.com. root.harkonen.it22.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      harkonen.it22.com.
@       IN      A       192.244.1.2
@       IN      AAAA    ::1' > /etc/bind/it22/harkonen.it22.com

echo "DNS configuration for atreides.it22.com has been set up."
```

<br />

Restart bind9
```
service bind9 restart
```



# [ 1 ]
> Lakukan konfigurasi sesuai dengan peta yang sudah diberikan.
> 
> Kemudian, karena masih banyak spice yang harus dikumpulkan, bantulah para aterides untuk bersaing dengan harkonen dengan kriteria berikut.:
> 
> Semua CLIENT harus menggunakan konfigurasi dari DHCP Server.


## Configure each node
### Arakis (Router/DHCP Relay)
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.244.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.244.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 192.244.3.1
	netmask 255.255.255.0
	
auto eth4
iface eth4 inet static
	address 192.244.4.1
	netmask 255.255.255.0
```

### Mohiam (DHCP Server) 
```
auto eth0
iface eth0 inet static
	address 192.244.3.2
	netmask 255.255.255.0
	gateway 192.244.3.1
```

### Irulan (DNS Server)
```
auto eth0
iface eth0 inet static
	address 192.244.3.3
	netmask 255.255.255.0
	gateway 192.244.3.1
```

### Chani (Database Server)
```
auto eth0
iface eth0 inet static
	address 192.244.4.2
	netmask 255.255.255.0
	gateway 192.244.4.1
```

### Stilgar (Load Balancer)
```
auto eth0
iface eth0 inet static
	address 192.244.4.3
	netmask 255.255.255.0
	gateway 192.244.4.1
```

### Leto (Laravel Worker)
```
auto eth0
iface eth0 inet static
	address 192.244.2.2
	netmask 255.255.255.0
	gateway 192.244.2.1
```

### Duncan (Laravel Worker)
```
auto eth0
iface eth0 inet static
	address 192.244.2.3
	netmask 255.255.255.0
	gateway 192.244.2.1
```

### Jessica (Laravel Worker)
```
auto eth0
iface eth0 inet static
	address 192.244.2.4
	netmask 255.255.255.0
	gateway 192.244.2.1
```

### Vladimir (PHP Worker)
```
auto eth0
iface eth0 inet static
	address 192.244.1.2
	netmask 255.255.255.0
	gateway 192.244.1.1
```

### Rabban (PHP Worker)
```
auto eth0
iface eth0 inet static
	address 192.244.1.3
	netmask 255.255.255.0
	gateway 192.244.1.1
```

### Feyd (PHP Worker)
```
auto eth0
iface eth0 inet static
	address 192.244.1.4
	netmask 255.255.255.0
	gateway 192.244.1.1
```

### Dmitri (Client)
```
auto eth0
iface eth0 inet dhcp
```

### Paul (Client) 
```
auto eth0
iface eth0 inet dhcp
```

# [ 2 - 5 ]
> - Client yang melalui House Harkonen mendapatkan range IP dari [prefix IP].1.14 - [prefix IP].1.28 dan [prefix IP].1.49 - [prefix IP].1.70
> 
> - Client yang melalui House Atreides mendapatkan range IP dari [prefix IP].2.15 - [prefix IP].2.25 dan [prefix IP].2 .200 - [prefix IP].2.210
> 
> - Client mendapatkan DNS dari Princess Irulan dan dapat terhubung dengan internet melalui DNS tersebut
> 
> - Durasi DHCP server meminjamkan alamat IP kepada Client yang melalui House Harkonen selama 5 menit sedangkan pada client yang melalui House Atreides selama 20 menit. Dengan waktu maksimal dialokasikan untuk peminjaman alamat IP selama 87 menit
> - *house == switch

## @ Mohiam (DHCP Server)
Update and install DHCP server, then check status to ensure it is installed.
```
apt-get update -y
apt-get install isc-dhcp-server -y
dhcpd --version
```


Specify network interface of DHCP server (in this case eth0).
```
echo '
INTERFACESv4="eth0"
INTERFACESv6=""
' > /etc/default/isc-dhcp-server
```


Configure DHCP server according to what the question specified.
```
echo 'subnet 192.244.1.0 netmask 255.255.255.0 {
    range 192.244.1.14 192.244.1.28;
    range 192.244.1.49 192.244.1.70;
    option routers 192.244.1.1;
    option broadcast-address 192.244.1.255;
    option domain-name-servers 192.244.3.3;
    default-lease-time 300;
    max-lease-time 5220;
}

subnet 192.244.2.0 netmask 255.255.255.0 {
    range 192.244.2.15 192.244.2.25;
    range 192.244.2.200 192.244.2.210;
    option routers 192.244.2.1;
    option broadcast-address 192.244.2.255;
    option domain-name-servers 192.244.3.3;
    default-lease-time 1200;
    max-lease-time 5220;
}

subnet 192.244.3.0 netmask 255.255.255.0 {
}' > /etc/dhcp/dhcpd.conf
```


After setting up the configurations, restart the DHCP server and check its status.
```
service isc-dhcp-server restart
service isc-dhcp-server status
```

## @ Irulan (DNS Server)
To allow clients to connect with Irulan (DNS Server), add forwarders.
```
echo '
options {
        directory "/var/cache/bind";

        forwarders {
            192.168.122.1;
        };

        allow-query{any;};

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};' > /etc/bind/named.conf.options
```


## Testing
To test out the configurations, launch each clients and ping to the domains specified at no. 1.


**@ Dmirtri (Client)**
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/a26254fc-c19a-4231-a791-4e1e6ad480cb)


**@ Paul (Client)**
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/cdfdee44-488d-40a9-bf8e-bd18e2bc91f4)

# [ 6 ]
> Seiring berjalannya waktu kondisi semakin memanas, untuk bersiap perang. Klan Harkonen melakukan deployment sebagai berikut
>
> Vladimir Harkonen memerintahkan setiap worker(harkonen) PHP, untuk melakukan konfigurasi virtual host untuk website berikut dengan menggunakan php 7.3.

## @ Harkonen (all PHP Workers)

Install dependencies
```
apt-get update -y
apt-get install php7.3-fpm php7.3-common php7.3-mysql php7.3-gmp php7.3-curl php7.3-intl php7.3-mbstring php7.3-xmlrpc php7.3-gd php7.3-xml php7.3-cli php7.3-zip -y
apt-get install nginx -y
apt-get install wget unzip -y
```

Create directory for deployment.
```
mkdir -p /var/www/harkonen.it22.com
```


Download deployment resource to the previously created folder.
```
wget --no-check-certificate 'https://docs.google.com/uc?export=download&id=1lmnXJUbyx1JDt2OA5z_1dEowxozfkn30' -O /tmp/modul-3.zip
unzip -o /tmp/modul-3.zip -d /tmp/
rm /tmp/modul-3.zip
mv /tmp/modul-3/* /var/www/harkonen.it22.com
```

Configure deployment as follows:
```
cp /etc/nginx/sites-available/default /etc/nginx/sites-available/harkonen.it22.com
ln -s /etc/nginx/sites-available/harkonen.it22.com /etc/nginx/sites-enabled/
rm /etc/nginx/sites-enabled/default

echo 'server {
    listen 80;
    server_name harkonen.it22.com;

    root /var/www/harkonen.it22.com;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.3-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}' > /etc/nginx/sites-available/harkonen.it22.com
```


Start nginx server.
```
service nginx start
service php7.3-fpm start
```


### Testing @ Vladimir (PHP Worker)
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/00751a0b-ca22-4fb9-8be9-0b8b7586e6d4)


### Testing @ Rabban (PHP Worker)
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/195f646d-2c36-4478-94f1-955386b82085)


### Testing @ Feyd (PHP Worker)
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/b683245e-0483-4134-a058-a69fad85c23f)



# [ 7 ]
> Aturlah agar Stilgar dari fremen dapat dapat bekerja sama dengan maksimal, lalu lakukan testing dengan 5000 request dan 150 request/second.


Install dependencies
```
apt-get update -y
apt-get install nginx -y
```


Configure load balancer, here round robin algorithm (default) will be used.
```
cp /etc/nginx/sites-available/default /etc/nginx/sites-available/lb_php

echo 'upstream roundrobin {
    server 192.244.1.2;
    server 192.244.1.3;
    server 192.244.1.4;
}

server {
    listen 80;
    server_name _;

    root /var/www/html;

    index index.html index.htm index.nginx-debian.html;

    location / {
        proxy_pass http://roundrobin;
    }
}' > /etc/nginx/sites-available/lb_php

ln -s /etc/nginx/sites-available/lb_php /etc/nginx/sites-enabled/
rm /etc/nginx/sites-enabled/default
```

Restart nginx server.
```
service nginx restart
```

Using apache benchmark, run the following command to test for 5000 requests and 150 concurrency.
```
ab -l -n 5000 -c 500 http://192.244.4.3/
```


### Benchmark Result
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/8688a959-890a-40c1-81f0-982c42ef8de2)



# [ 8 ]
> Karena diminta untuk menuliskan peta tercepat menuju spice, buatlah analisis hasil testing dengan 500 request dan 50 request/second masing-masing algoritma Load Balancer dengan ketentuan sebagai berikut:
> 
> Nama Algoritma Load Balancer
> 
> Report hasil testing pada Apache Benchmark
> 
> Grafik request per second untuk masing masing algoritma.
> 
> Analisis


## @ Stilgar (Load Balancer)
Configure endpoints to test out the different algorithms:
```
echo 'upstream roundrobin {
    server 192.244.1.2;
    server 192.244.1.3;
    server 192.244.1.4;
}

upstream leastconn3 {
    least_conn;
    server 192.244.1.2;
    server 192.244.1.3;
    server 192.244.1.4;
}

upstream iphash {
    ip_hash;
    server 192.244.1.2;
    server 192.244.1.3;
    server 192.244.1.4;
}

upstream genhash {
    hash $remote_addr consistent;
    server 192.244.1.2;
    server 192.244.1.3;
    server 192.244.1.4;
}

server {
    listen 80;
    server_name _;

    root /var/www/html;

    index index.html index.htm index.nginx-debian.html;

    location / {
        proxy_pass http://roundrobin;
    }

    location /leastconn3 {
        proxy_pass http://leastconn3;
    }

    location /iphash {
        proxy_pass http://iphash;
    }

    location /genhash {
        proxy_pass http://genhash;
    }
}' > /etc/nginx/sites-available/lb_php
```


Restart nginx server.
```
service nginx restart
```


Using apache benchmark, run the following command to test for 500 requests and 50 concurrency.

```
ab -l -n 500 -c 50 http://192.244.4.3/<endpoint>
```
Note: ```<endpoint>``` should be replaced with the algorithm endpoint that wants to be used.


## Benchmark Results
### Round robin
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/6293de56-758c-4766-9a30-5e9f9c73ace7)


### Least connection
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/3a53df96-7452-41f2-a8a4-94d94398901f)


### IP Hash
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/f66f2092-bf35-4aa8-91a4-0e71b5241942)


### Generic Hash
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/cb9dc010-6bcd-4afe-9c61-7377309efbae)


### Comparison Graph
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/a8535ac3-f686-4686-9e9f-4d80a70eee9f)



# [ 9 ]
> Dengan menggunakan algoritma Least-Connection, lakukan testing dengan menggunakan 3 worker, 2 worker, dan 1 worker sebanyak 1000 request dengan 10 request/second, kemudian tambahkan grafiknya pada peta.


Configure endpoints to test out the different number of workers:
```
echo 'upstream leastconn3 {
    least_conn;
    server 192.244.1.2;
    server 192.244.1.3;
    server 192.244.1.4;
}

upstream leastconn2 {
    least_conn;
    server 192.244.1.2;
    server 192.244.1.3;
}

upstream leastconn1 {
    least_conn;
    server 192.244.1.2;
}

server {
    listen 80;
    server_name _;

    root /var/www/html;

    index index.html index.htm index.nginx-debian.html;

    location /leastconn3 {
        proxy_pass http://leastconn3;
    }

    location /leastconn2 {
        proxy_pass http://leastconn2;
    }

    location /leastconn1 {
        proxy_pass http://leastconn1;
    }
}' > /etc/nginx/sites-available/lb_php
```


Restart nginx server.
```
service nginx restart
```


Using apache benchmark, run the following command to test for 500 requests and 50 concurrency.

```
ab -l -n 1000 -c 10 http://192.244.4.3/<endpoint>
```
Note: ```<endpoint>``` should be replaced with the number of workers endpoint that wants to be used.


### 3 Workers

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/d380dbf9-01d0-4811-9f01-0fb2fa8621b2)


### 2 Workers

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/af8ca8e7-1c8f-428c-a904-0fe4c7db3bdf)


### 1 Worker

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/2c463eb2-fc7a-41c5-a830-0d44f47e8359)


### Comparison Graph

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/baaa25f9-84be-4e21-a806-cf576a463e92)




# [ 10 ]
> Selanjutnya coba tambahkan keamanan dengan konfigurasi autentikasi di LB dengan dengan kombinasi username: “secmart” dan password: “kcksyyy”, dengan yyy merupakan kode kelompok.
> 
> Terakhir simpan file “htpasswd” nya di /etc/nginx/supersecret/


Configure authentication 
```
echo 'upstream roundrobin {
    server 192.244.1.2;
    server 192.244.1.3;
    server 192.244.1.4;
}

server {
    listen 80;
    server_name _;

    root /var/www/html;

    index index.html index.htm index.nginx-debian.html;

    location / {
        proxy_pass http://roundrobin;
        auth_basic "Restricted Content";
        auth_basic_user_file /etc/nginx/supersecret/htpasswd;
    }
}' > /etc/nginx/sites-available/lb_php
```


Create supersecret directory.

```
mkdir /etc/nginx/supersecret
```

Run htpasswd command and set username to secmart and kcksit22 as its password.

```
htpasswd -c /etc/nginx/supersecret/htpasswd secmart
```


## Testing @ Dmitri (Client)

As seen from screenshots below, authentication is needed to access the deployed website.


![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/991f94d8-be97-4195-b723-95bc238d3665)


![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/b2c1117a-52b6-49ad-8adb-f7d8d40871cc)


![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/982c1e27-c036-45fd-9d1f-54df89bcca84)


# [ 11 ]
> Lalu buat untuk setiap request yang mengandung /dune akan di proxy passing menuju halaman https://www.dunemovie.com.au/.

```
echo 'upstream roundrobin {
    server 192.244.1.2;
    server 192.244.1.3;
    server 192.244.1.4;
}

server {
    listen 80;
    server_name _;

    root /var/www/html;

    index index.html index.htm index.nginx-debian.html;

    location / {
        proxy_pass http://roundrobin;
        auth_basic "Restricted Content";
        auth_basic_user_file /etc/nginx/supersecret/htpasswd;
    }

    location /dune {
        proxy_pass https://www.dunemovie.com.au/;
    }
}' > /etc/nginx/sites-available/lb_php
```


## Testing @ Dmitri (Client)
```
lynx http://192.244.4.3/dune
```

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/82570f29-bf2b-4efb-9a14-7bf86a1e045b)


# [ 12 ] 
> Selanjutnya LB ini hanya boleh diakses oleh client dengan IP [Prefix IP].1.37, [Prefix IP].1.67, [Prefix IP].2.203, dan [Prefix IP].2.207.

### 
To test out the configuration, first configure fixed IP address


![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/671246d7-8f30-4658-a62f-2c850ac47212)


Then try to access the website.

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/00751a0b-ca22-4fb9-8be9-0b8b7586e6d4)


# [ 13 ]
> Semua data yang diperlukan, diatur pada Chani dan harus dapat diakses oleh Leto, Duncan, dan Jessica.

## @ Chani (Database Server)

Install dependencies.

```
apt-get update
apt-get install mariadb-server -y
service mysql start
```


Configure to ensure that MySQL will listen for network connections.
```
echo '# This group is read both by the client and the server
# use it for options that affect everything
[client-server]

# Import all .cnf files from configuration directory
!includedir /etc/mysql/conf.d/
!includedir /etc/mysql/mariadb.conf.d/

# Options affecting the MySQL server (mysqld)
[mysqld]
skip-networking=0
skip-bind-address
' > /etc/mysql/my.cnf 
```


Allow connection from anywhere.
```
sed -i 's/127.0.0.1/0.0.0.0/g' /etc/mysql/mariadb.conf.d/50-server.cnf
```


Restart mysql.
```
service mysql restart
```


Create user and database.
```
mysql -u root -p
```

Do the following command at mysql:
```
CREATE USER 'jac'@'%' IDENTIFIED BY 'passjac';
CREATE USER 'jac'@'localhost' IDENTIFIED BY 'passjac';
CREATE DATABASE dbjac;
GRANT ALL PRIVILEGES ON *.* TO 'jac'@'%';
GRANT ALL PRIVILEGES ON *.* TO 'jac'@'localhost';
FLUSH PRIVILEGES;
```

## Testing @ Atreides (Laravel Workers)


Use the following command to access the database
```
mariadb --host=192.244.4.2 --port=3306 --user=jac --password=passjac dbjac -e "SHOW DATABASES;"
```


### Result

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/08638f4b-6837-4478-84cb-68add56d54e9)


# [ 14 ] 
> Leto, Duncan, dan Jessica memiliki atreides Channel sesuai dengan quest guide berikut. Jangan lupa melakukan instalasi PHP8.0 dan Composer

## @ Atreides (Laravel Workers)

Install dependencies.
```
apt-get update
apt-get install lynx -y
apt-get install mariadb-client -y
apt-get install -y lsb-release ca-certificates apt-transport-https software-properties-common gnupg2
curl -sSLo /usr/share/keyrings/deb.sury.org-php.gpg https://packages.sury.org/php/apt.gpg
sh -c 'echo "deb [signed-by=/usr/share/keyrings/deb.sury.org-php.gpg] https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list'
apt-get update
apt-get install php8.0-mbstring php8.0-xml php8.0-cli   php8.0-common php8.0-intl php8.0-opcache php8.0-readline php8.0-mysql php8.0-fpm php8.0-curl unzip wget -y
apt-get install nginx -y
apt install apache2-utils -y

wget https://getcomposer.org/download/2.0.13/composer.phar
chmod +x composer.phar
mv composer.phar /usr/local/bin/composer

apt-get install git -y
```


Clone the quest guide to /var/www/
```
cd /var/www && git clone https://github.com/martuafernando/laravel-praktikum-jarkom
cd /var/www/laravel-praktikum-jarkom && composer update
```


Configure .env with credentials made before.

```
echo 'APP_NAME=Laravel
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost

LOG_CHANNEL=stack
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=192.244.4.2
DB_PORT=3306
DB_DATABASE=dbjac
DB_USERNAME=jac
DB_PASSWORD=passjac

BROADCAST_DRIVER=log
CACHE_DRIVER=file
FILESYSTEM_DISK=local
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120

MEMCACHED_HOST=127.0.0.1

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS="hello@example.com"
MAIL_FROM_NAME="${APP_NAME}"

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=
AWS_USE_PATH_STYLE_ENDPOINT=false

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
PUSHER_HOST=
PUSHER_PORT=443
PUSHER_SCHEME=https
PUSHER_APP_CLUSTER=mt1

VITE_PUSHER_APP_KEY="${PUSHER_APP_KEY}"
VITE_PUSHER_HOST="${PUSHER_HOST}"
VITE_PUSHER_PORT="${PUSHER_PORT}"
VITE_PUSHER_SCHEME="${PUSHER_SCHEME}"
VITE_PUSHER_APP_CLUSTER="${PUSHER_APP_CLUSTER}"' > /var/www/laravel-praktikum-jarkom/.env
```

Configure laravel application.

```
cd /var/www/laravel-praktikum-jarkom && php artisan config:cache
cd /var/www/laravel-praktikum-jarkom && php artisan migrate:fresh
cd /var/www/laravel-praktikum-jarkom && php artisan db:seed --class=AiringsTableSeeder
cd /var/www/laravel-praktikum-jarkom && php artisan key:generate
cd /var/www/laravel-praktikum-jarkom && php artisan storage:link
cd /var/www/laravel-praktikum-jarkom && php artisan jwt:secret
cd /var/www/laravel-praktikum-jarkom && php artisan config:clear
```


Configure each worker with their assigned port. Replace ```<port>``` with the assigned port.
```
echo 'server {
    listen <port>;

    root /var/www/laravel-praktikum-jarkom/public;

    index index.php index.html index.htm;
    server_name _;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    # pass PHP scripts to FastCGI server
    location ~ \.php$ {
      include snippets/fastcgi-php.conf;
      fastcgi_pass unix:/var/run/php/php8.0-fpm.sock;
    }

    location ~ /\.ht {
            deny all;
    }

    error_log /var/log/nginx/implementasi_error.log;
    access_log /var/log/nginx/implementasi_access.log;
}' > /etc/nginx/sites-available/laravel-worker
```


Restart nginx server.
```
service nginx restart
```

## Testing @ Atreides (Laravel Worker)

Test by accessing localhost

```
lynx http://localhost:8001
```

### Result
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/f13fd442-2d09-45cb-894d-ae170672b93f)


# [ 15, 16 ]
> atreides Channel memiliki beberapa endpoint yang harus ditesting sebanyak 100 request dengan 10 request/second. Tambahkan response dan hasil testing pada peta.
> 
> POST /auth/register (15)
> 
> POST /auth/login (16)

Check documentation/source code to create an appropriate body for the POST requests.


Create register.json as the body to be tested:
```
{
  "username": "jac",
  "password": "passjac"
}
```


## @ Paul (Laravel Worker)
### POST req to /auth/register:
```
ab -l -n 100 -c 10 -p register.json -T application/json http://localhost:8001/api/auth/register
```
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/0e315c00-0522-4580-84f0-c6fec455f9c9)

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/69485d01-dcbb-484f-873f-94a5940c26e7)


### POST req to /auth/login:
```
ab -l -n 100 -c 10 -p register.json -T application/json http://localhost:8001/api/auth/login
```
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/4a3ebb0a-eafe-40f8-8285-3974879c6a3e)

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/86682648-9782-40bd-9746-fb02166f9c96)



# [ 17 ]
> GET /me


## @ Atreides (any Laravel Worker)

Get token 

```
curl -X POST -H "Content-Type: application/json" -d @register.json http://localhost:8001/api/auth/login > login_output.txt
```

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/5b6f2467-952e-4326-8ae9-d309f470c8ef)


### GET req to /me

```
 ab -l -n 100 -c 10 -H "Authorization: Bearer $(cat login_output.txt | jq -r '.token')" http://localhost:8001/api/me
```

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/5f78f694-4cd8-4110-bda8-8ad45f5e3d0f)


![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/06fb08f4-0de4-4b55-b7bf-aa70413a1989)


# [ 18 ] 
> Untuk memastikan ketiganya bekerja sama secara adil untuk mengatur atreides Channel maka implementasikan Proxy Bind pada Stilgar untuk mengaitkan IP dari Leto, Duncan, dan Jessica.


## @ Stilgar (Load Balancer)
```
echo 'upstream worker {
    # least_conn;
    server 192.244.2.2:8001;
    server 192.244.2.3:8002;
    server 192.244.2.4:8003;
}

server {
    listen 80;
    server_name _;

    location / {
        proxy_pass http://worker;
    }
} 
'> /etc/nginx/sites-available/laravel-worker

ln -s /etc/nginx/sites-available/laravel-worker /etc/nginx/sites-enabled/laravel-worker
rm /etc/nginx/sites-enabled/default
```

Restart nginx server

```
service nginx restart
```


## Testing @ Paul (Client)
```
ab -l -n 100 -c 10 -p register.json -T application/json http://localhost:8001/api/auth/register
```

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/5ecb39f5-39b9-4e98-91f3-4a1ca7ffd2af)


As seen here, each workers are taking the request.

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/be27b5f5-9001-4a77-88b5-0a0c533897d5)



# [ 19 ]
> Untuk meningkatkan performa dari Worker, coba implementasikan PHP-FPM pada Leto, Duncan, dan Jessica. Untuk testing kinerja naikkan
> 
> - pm.max_children
>   
> - pm.start_servers
>   
> - pm.min_spare_servers
>   
> - pm.max_spare_servers
>   
> sebanyak tiga percobaan dan lakukan testing sebanyak 100 request dengan 10 request/second kemudian berikan hasil analisisnya pada PDF.


## @ Atreides (Laravel Workers)

### Configuration 1 (Low Load, Low Resource)

```
echo '[www]
user = www-data
group = www-data
listen = /run/php/php8.0-fpm.sock
listen.owner = www-data
listen.group = www-data
php_admin_value[disable_functions] = exec,passthru,shell_exec,system
php_admin_flag[allow_url_fopen] = off

; Choose how the process manager will control the number of child processes.

pm = dynamic
pm.max_children = 5
pm.start_servers = 2
pm.min_spare_servers = 1
pm.max_spare_servers = 3' > /etc/php/8.0/fpm/pool.d/www.conf

service php8.0-fpm restart

# Low Load, Low Resource
```

### Configuration 2 (Moderate Load, Moderate Resource)

```
echo '[www]
user = www-data
group = www-data
listen = /run/php/php8.0-fpm.sock
listen.owner = www-data
listen.group = www-data
php_admin_value[disable_functions] = exec,passthru,shell_exec,system
php_admin_flag[allow_url_fopen] = off

; Choose how the process manager will control the number of child processes.

pm = dynamic
pm.max_children = 30
pm.start_servers = 5
pm.min_spare_servers = 3
pm.max_spare_servers = 10' > /etc/php/8.0/fpm/pool.d/www.conf

service php8.0-fpm restart

# Moderate Load, Moderate Resource
```


### Configuration 3 (High Load, High Resource)
```
echo '[www]
user = www-data
group = www-data
listen = /run/php/php8.0-fpm.sock
listen.owner = www-data
listen.group = www-data
php_admin_value[disable_functions] = exec,passthru,shell_exec,system
php_admin_flag[allow_url_fopen] = off

; Choose how the process manager will control the number of child processes.

pm = dynamic
pm.max_children = 60
pm.start_servers = 10
pm.min_spare_servers = 5
pm.max_spare_servers = 15' > /etc/php/8.0/fpm/pool.d/www.conf

service php8.0-fpm restart

# High Load, High Resource
```

## Testing @ Paul (Client)

### Configuration 1 Result
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/18c73cfe-d6d5-416f-8478-49799cf0820a)


### Configuration 2 Result
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/1d9142f9-b682-4b61-be0d-f670137edf38)


### Configuration 3 Result
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/20963ed8-9a4a-4854-8761-ce1aec51993e)


### Comparison Graph Result
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/45c7b711-8b2e-4075-9af1-d37840ac68d4)


# [ 20 ] 
> Nampaknya hanya menggunakan PHP-FPM tidak cukup untuk meningkatkan performa dari worker maka implementasikan Least-Conn pada Stilgar.
> 
> Untuk testing kinerja dari worker tersebut dilakukan sebanyak 100 request dengan 10 request/second.

## @ Stilgar (Load Balancer)

Configure least connection algorithm.

```
echo 'upstream worker {
    least_conn;
    server 192.244.2.2:8001;
    server 192.244.2.3:8002;
    server 192.244.2.4:8003;
}

server {
    listen 80;
    server_name _;

    location / {
        proxy_pass http://worker;
    }
} 
'> /etc/nginx/sites-available/laravel-worker

ln -s /etc/nginx/sites-available/laravel-worker /etc/nginx/sites-enabled/laravel-worker
rm /etc/nginx/sites-enabled/default
```

Restart nginx server.

```
service nginx restart
```

## Testing @ Paul (Client)
```
ab -l -n 100 -c 10 http://192.244.4.3/
```
### Result


![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/5064778f-266c-44a5-9fcb-ff445470a9ea)
