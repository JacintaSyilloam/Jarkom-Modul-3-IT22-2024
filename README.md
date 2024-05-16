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
};' >> /etc/bind/named.conf.local

echo '
zone "harkonen.it22.com" {
	type master;
	file "/etc/bind/it22/harkonen.it22.com";
};' > /etc/bind/named.conf.local
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
> Kemudian, karena masih banyak spice yang harus dikumpulkan, bantulah para aterides untuk bersaing dengan harkonen dengan kriteria berikut.:
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
> Client yang melalui House Harkonen mendapatkan range IP dari [prefix IP].1.14 - [prefix IP].1.28 dan [prefix IP].1.49 - [prefix IP].1.70
> Client yang melalui House Atreides mendapatkan range IP dari [prefix IP].2.15 - [prefix IP].2.25 dan [prefix IP].2 .200 - [prefix IP].2.210
> Client mendapatkan DNS dari Princess Irulan dan dapat terhubung dengan internet melalui DNS tersebut
> Durasi DHCP server meminjamkan alamat IP kepada Client yang melalui House Harkonen selama 5 menit sedangkan pada client yang melalui House Atreides selama 20 menit. Dengan waktu maksimal dialokasikan untuk peminjaman alamat IP selama 87 menit
> *house == switch
