
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
#### Comprobación con comando:
```
root@darthvader:/# ip a | grep 192.168.20.1
    inet 192.168.20.10/24 brd 192.168.20.255 scope global eth0
```
### Imaxe da preinstalación co DockerFIle

### 5º Usar unha única interface de rede
### Imaxe da preinstalación co DockerFIle
![DockerFile dns](/docs/img/dockerfilepre.png)

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
```
options {
	directory "/var/cache/bind";

	// If there is a firewall between you and nameservers you want
	// to talk to, you may need to fix the firewall to allow multiple
	// ports to talk.  See http://www.kb.cert.org/vuls/id/800113

	// If your ISP provided one or more IP addresses for stable 
	// nameservers, you probably want to use them as forwarders.  
	// Uncomment the following block, and insert the addresses replacing 
	// the all-0's placeholder.

	forwarders {
		8.8.8.8;
	};

	//========================================================================
	// If BIND logs error messages about the root key being expired,
	// you will need to update your keys.  See https://www.isc.org/bind-keys
	//========================================================================
	dnssec-validation no;

	listen-on-v6 { none; };
};
```

#### Comando ejecutado:
```bash
dig @localhost www.mecd.gob.es
```

#### Salida del comando:
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
$TTL 86400                ; Tiempo de vida en segundos
@   IN  SOA darthvader.starwars.lan. admin.starwars.lan. (
        2023101001 ; Número de serie (cambia este número cuando hagas cambios)
        604800      ; Refresh (intervalo de actualización)
        86400       ; Retry (intervalo de reintento)
        2419200     ; Expire (tiempo antes de que la zona expire)
        604800 )     ; Minimum TTL (TTL mínimo para los registros de la zona)

; Servidores de nombres para la zona
@   IN  NS  darthsidious.starwars.lan.

; Hosts Tipo A
darthvader   IN  A    192.168.20.10
skywalker     IN  A    192.168.20.101
skywalker     IN  A    192.168.20.111
luke         IN  A    192.168.20.22
darthsidious IN  A    192.168.20.11
yoda         IN  A    192.168.20.24
yoda         IN  A    192.168.20.25
c3p0         IN  A    192.168.20.26

; Hosts tipo CNAME 
palpatine    IN  CNAME darthsidious.starwars.lan.

; Hosts tipo MX 
@            IN  MX   10 c3p0.starwars.lan.

; Hosts tipo TXT (información adicional)
lenda        IN  TXT  "Que a forza te acompanhe"
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
(Pega aquí el contenido del archivo de zona)
```

#### Contenido del archivo `/etc/bind/named.conf.local`:
```
(Pega aquí el contenido del archivo)
```

### 5. Comprobación de la resolución de registros

#### Comandos ejecutados y sus salidas:
```bash
nslookup darthvader.starwars.lan localhost
```
```
(Pega aquí la salida del comando)
```


