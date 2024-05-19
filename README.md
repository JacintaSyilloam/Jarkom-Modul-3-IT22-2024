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


## Testing
**@ Dmirtri (Client)**
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/a26254fc-c19a-4231-a791-4e1e6ad480cb)


**@ Paul (Client)**
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/cdfdee44-488d-40a9-bf8e-bd18e2bc91f4)

# [ 6 ]
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/00751a0b-ca22-4fb9-8be9-0b8b7586e6d4)
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/195f646d-2c36-4478-94f1-955386b82085)
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/b683245e-0483-4134-a058-a69fad85c23f)


# [ 7 ]
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/8688a959-890a-40c1-81f0-982c42ef8de2)


# [ 8 ]
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
### 3 Workers
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/d380dbf9-01d0-4811-9f01-0fb2fa8621b2)


### 2 Workers
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/af8ca8e7-1c8f-428c-a904-0fe4c7db3bdf)


### 1 Worker
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/2c463eb2-fc7a-41c5-a830-0d44f47e8359)

### Comparison Graph
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/baaa25f9-84be-4e21-a806-cf576a463e92)



# [ 10 ]
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/991f94d8-be97-4195-b723-95bc238d3665)
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/b2c1117a-52b6-49ad-8adb-f7d8d40871cc)
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/982c1e27-c036-45fd-9d1f-54df89bcca84)


# [ 11 ]
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/82570f29-bf2b-4efb-9a14-7bf86a1e045b)


# [ 12 ] 
### 
Configure fixed IP address
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/671246d7-8f30-4658-a62f-2c850ac47212)


# [ 13 ]
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/08638f4b-6837-4478-84cb-68add56d54e9)


# [ 14 ] 
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/f13fd442-2d09-45cb-894d-ae170672b93f)

# [ 15, 16 ]
Check documentation/source code to create an appropriate body for the POST requests.


register.json:
```
{
  "username": "jac",
  "password": "passjac"
}
```


## @ Paul (Laravel Worker)
## POST req to /auth/register:
```
ab -l -n 100 -c 10 -p register.json -T application/json http://localhost:8001/api/auth/register
```
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/0e315c00-0522-4580-84f0-c6fec455f9c9)

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/69485d01-dcbb-484f-873f-94a5940c26e7)


## POST req to /auth/login:
```
ab -l -n 100 -c 10 -p register.json -T application/json http://localhost:8001/api/auth/login
```
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/4a3ebb0a-eafe-40f8-8285-3974879c6a3e)

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/86682648-9782-40bd-9746-fb02166f9c96)


# [ 17 ]
Get token 
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/5b6f2467-952e-4326-8ae9-d309f470c8ef)
```
 ab -l -n 100 -c 10 -H "Authorization: Bearer $(cat login_output.txt | jq -r '.token')" http://localhost:8001/api/me
```
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/5f78f694-4cd8-4110-bda8-8ad45f5e3d0f)

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/06fb08f4-0de4-4b55-b7bf-aa70413a1989)


# [ 18 ] 

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/5ecb39f5-39b9-4e98-91f3-4a1ca7ffd2af)

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/be27b5f5-9001-4a77-88b5-0a0c533897d5)



# [ 19 ]

### Config 1
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/18c73cfe-d6d5-416f-8478-49799cf0820a)


### Config 2
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/1d9142f9-b682-4b61-be0d-f670137edf38)


### Config 3
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/20963ed8-9a4a-4854-8761-ce1aec51993e)

# [ 20 ] 
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-3-IT22-2024/assets/121095246/5064778f-266c-44a5-9fcb-ff445470a9ea)
