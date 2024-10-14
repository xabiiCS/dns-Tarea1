
# Entrega

## Introducción

En este documento se detallan los pasos necesarios para la configuración de un servidor DNS utilizando BIND9. A lo largo de esta guía, se explicará cómo instalar y configurar el servidor, así como la creación de zonas de resolución directa e inversa. Además, se proporcionarán ejemplos de comandos y sus salidas para verificar el correcto funcionamiento del servidor DNS. Este trabajo es parte de la entrega 1 de la asignatura de Sistemas y Redes de Información (SRI).

## Requisitos Previos á tarea

### 1º Temos acceso a Internet?

#### Comprobación con comando:

```
root@darthvader:/# ping google.com.
PING google.com (142.250.201.78) 56(84) bytes of data.
64 bytes from mad07s25-in-f14.1e100.net (142.250.201.78): icmp_seq=1 ttl=115 time=13.2 ms
64 bytes from mad07s25-in-f14.1e100.net (142.250.201.78): icmp_seq=2 ttl=115 time=12.1 ms
^C
--- google.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 12.052/12.650/13.249/0.598 ms
```

### 2º O Nome do host DNS é ***dartvader***?

#### Comprobación con comando:

```
root@darthvader:/# hostname
darthvader
```

### 3º A súa direccion IPv4 é 192.168.20.10/24?

#### Comprobación con comando:

```
root@darthvader:/# ip a | grep 192.168.20.1
    inet 192.168.20.10/24 brd 192.168.20.255 scope global eth0
```
### 4º Instalar paquete dnsutils
#### Comprobación con comando e Imaxe da preinstalación co DockerFIle
![DockerFile dns](/docs/img/dockerfilepre.png)

```
root@darthvader:/# dpkg -l | grep dnsutils
ii  bind9-dnsutils          1:9.18.28-1~deb12u2     amd64        Clients provided with BIND 9
ii  dnsutils                1:9.18.28-1~deb12u2     all          Transitional package for bind9-dnsutils
```
<br>

### 5º Usar unha única interface de rede
![DockerFile dns](/docs/img/compose.png)


## Respostas á Tarefa

### 1. Funciona o servidor BIND9

#### Comando executado para a proba:
```bash
dig @localhost www.edu.xunta.gal
```

#### Salida del comando:
```
root@darthvader:/# dig @localhost www.edu.xunta.gal

; <<>> DiG 9.18.28-1~deb12u2-Debian <<>> @localhost www.edu.xunta.gal
; (2 servers found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 44367
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: f087713a17d56d25010000006707b428bc743d91cf4f84c0 (good)
;; QUESTION SECTION:
;www.edu.xunta.gal.		IN	A

;; ANSWER SECTION:
www.edu.xunta.gal.	16913	IN	A	85.91.64.65

;; Query time: 11 msec
;; SERVER: 127.0.0.1#53(localhost) (UDP)
;; WHEN: Thu Oct 10 11:02:00 UTC 2024
;; MSG SIZE  rcvd: 90
```

### 2. Configuración del reenviador

#### Contenido del archivo `/etc/bind/named.conf.options`:
![DockerFile dns](/docs/img/contenidoFORWARDERSSSS.png)

```
root@darthvader:/# dig @localhost www.mecd.gob.es

; <<>> DiG 9.18.28-1~deb12u2-Debian <<>> @localhost www.mecd.gob.es
; (2 servers found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 43563
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: f8a717b832c64c76010000006707b484d99d49189d1aa4a4 (good)
;; QUESTION SECTION:
;www.mecd.gob.es.		IN	A

;; ANSWER SECTION:
www.mecd.gob.es.	21600	IN	A	212.128.114.116

;; Query time: 15 msec
;; SERVER: 127.0.0.1#53(localhost) (UDP)
;; WHEN: Thu Oct 10 11:03:32 UTC 2024
;; MSG SIZE  rcvd: 88
```

### 3. Creación de una zona primaria de resolución directa

#### Contenido del archivo de zona:
```
$TTL 86400
@   IN  SOA darthvader.starwars.lan. admin.starwars.lan. (
     2023101001 ; Serial
     604800      ; Refresh
     86400       ; Retry
     2419200     ; Expire
     604800 )    ; Minimum TTL

@   IN  NS  darthvader.starwars.lan.
@   IN  NS  darthsidious.starwars.lan.   

; Registros A
darthvader   IN  A    192.168.20.10
skywalker    IN  A    192.168.20.101
skywalker    IN  A    192.168.20.111
luke         IN  A    192.168.20.22
darthsidious IN  A    192.168.20.11
yoda         IN  A    192.168.20.24
yoda         IN  A    192.168.20.25
c3p0         IN  A    192.168.20.26


; Registro TXT
lenda        IN  TXT  "Que a forza te acompanhe"

; Registro CNAME
palpatine    IN  CNAME darthsidious.starwars.lan.

; Registro MX
@            IN  MX   10 c3p0.starwars.lan.
```

#### Contenido del archivo `/etc/bind/named.conf.local`:
```
//
// Do any local configuration here

zone "starwars.lan" {
    type master;                 
    file "/etc/bind/db.starwars.lan"; 
};

//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";
```

### 4. Creación de una zona de resolución inversa

#### Contenido del archivo de zona:
```
$TTL 86400
@   IN  SOA darthvader.starwars.lan. admin.starwars.lan. (
        2023101001 ; Serial
        604800      ; Refresh
        86400       ; Retry
        2419200     ; Expire
        604800 )    ; Minimum TTL

; Servidores de nombres para la zona
@   IN  NS  darthsidious.starwars.lan.

; Registros PTR
10  IN  PTR darthvader.starwars.lan.
101 IN  PTR skywalker.starwars.lan.
111 IN  PTR skywalker.starwars.lan.
22  IN  PTR luke.starwars.lan.
11  IN  PTR darthsidious.starwars.lan.
24  IN  PTR yoda.starwars.lan.
25  IN  PTR yoda.starwars.lan.
26  IN  PTR c3p0.starwars.lan.
```

#### Contenido del archivo `/etc/bind/named.conf.local`:
```
//
// Do any local configuration here

zone "starwars.lan" {
    type master;                 
    file "/etc/bind/db.starwars.lan"; 
};

zone "20.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/db.192.168.20";
};

//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";

```

### 5. Comprobación de la resolución de registros

#### Comandos ejecutados y sus salidas:
```bash
     nslookup darthvader.starwars.lan localhost
```
![DockerFile dns](/docs/img/contenidoFORWARDERSSSS.png)
```bash
     nslookup skywalker.starwars.lan localhost
```
![DockerFile dns](/docs/img/contenidoFORWARDERSSSS.png)
```bash
     nslookup starwars.lan localhost
```
![DockerFile dns](/docs/img/contenidoFORWARDERSSSS.png)
```bash
     nslookup -q=mx starwars.lan localhost
```
![DockerFile dns](/docs/img/contenidoFORWARDERSSSS.png)
```bash
     nslookup -q=ns starwars.lan localhost
```
![DockerFile dns](/docs/img/contenidoFORWARDERSSSS.png)
```bash
     nslookup -q=soa starwars.lan localhost
```
![DockerFile dns](/docs/img/contenidoFORWARDERSSSS.png)
```bash
     nslookup -q=txt lenda.starwars.lan localhost
```
![DockerFile dns](/docs/img/contenidoFORWARDERSSSS.png)
```bash
     nslookup 192.168.20.11 localhost
```
![DockerFile dns](/docs/img/contenidoFORWARDERSSSS.png)

