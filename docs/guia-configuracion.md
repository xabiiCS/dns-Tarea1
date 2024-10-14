
# Entrega

## Introducción

En este documento se detallan los pasos necesarios para la configuración de un servidor DNS utilizando BIND9. A lo largo de esta guía, se explicará cómo instalar y configurar el servidor, así como la creación de zonas de resolución directa e inversa. Además, se proporcionarán ejemplos de comandos y sus salidas para verificar el correcto funcionamiento del servidor DNS. Este trabajo es parte de la entrega 1 de la asignatura de Sistemas y Redes de Información (SRI).

## Requisitos Previos á tarea

### 1º Temos acceso a Internet?

#### Comprobación con comando:

![Acceso a Internet?](/docs/img/salidas/pre1.png)

### 2º O Nome do host DNS é ***dartvader***?

#### Comprobación con comando:

![HOSTNAME](/docs/img/salidas/pre2.png)

### 3º A súa direccion IPv4 é 192.168.20.10/24?

#### Comprobación con comando:

![DockerFile dns](/docs/img/salidas/pre3.png)

### 4º Instalar paquete dnsutils
#### Comprobación con comando e Imaxe da preinstalación co DockerFIle

```bash
FROM debian:12

RUN apt update && apt install -y net-tools procps most iproute2  iputils-ping dnsutils

RUN apt install -y bind9 dnsutils rsyslog

RUN apt clean && rm -rf /var/lib/apt/lists/*

COPY entrypoint.sh /entrypoint.sh

RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
```


![DockerFile dns](/docs/img/salidas/pre4.png)


<br>

### 5º Usar unha única interface de rede
```bash
services:
  dnsserver:
    build: dnsserver
    hostname: darthvader
    networks:
      rededns:
          ipv4_address: 192.168.20.10
    volumes:
      - ./dnsserver/conf/db.192.168.20:/etc/bind/db.192.168.20
      - ./dnsserver/conf/db.local:/etc/bind/db.local
      - ./dnsserver/conf/db.starwars.lan:/etc/bind/db.starwars.lan
      - ./dnsserver/conf/named.conf.local:/etc/bind/named.conf.local
      - ./dnsserver/conf/named.conf.options:/etc/bind/named.conf.options
  client1:  
    build: debian
    hostname: eq1
    networks:
      rededns:
          ipv4_address: 192.168.20.100
networks:
  rededns:
    ipam:
      config:
        - subnet: 192.168.20.0/24
```


## Respostas á Tarefa

### 1. Funciona o servidor BIND9

#### Comando executado para a proba:
```bash
dig @localhost www.edu.xunta.gal
```

#### Salida del comando:
![DockerFile dns](/docs/img/salidas/ej1.png)

### 2. Configuración del reenviador

#### Contenido del archivo `/etc/bind/named.conf.options`:
``` bash
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
	forward only;

	//========================================================================
	// If BIND logs error messages about the root key being expired,
	// you will need to update your keys.  See https://www.isc.org/bind-keys
	//========================================================================
	dnssec-validation no;

	listen-on-v6 { none; };
};
```

![DockerFile dns](/docs/img/salidas/ej2.png)

### 3. Creación de una zona primaria de resolución directa

#### Contenido del archivo de zona:
``` bash
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
``` bash
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

### 4. Creación de una zona de resolución inversa

#### Contenido del archivo de zona:
``` bash
$TTL 86400
@   IN  SOA darthvader.starwars.lan. admin.starwars.lan. (
                  1   ; Serial 
             604800   ; Refresh
              86400   ; Retry
            2419200   ; Expire
             120 )    ; Minimum TTL

; Servidores de nombres para la zona
@   IN  NS  darthvader.starwars.lan.
@   IN  NS  darthsidious.starwars.lan.

; Registros PTR
10   IN  PTR darthvader.starwars.lan.
101  IN  PTR skywalker.starwars.lan.
111  IN  PTR skywalker.starwars.lan.
22   IN  PTR luke.starwars.lan.
11   IN  PTR darthsidious.starwars.lan.
24   IN  PTR yoda.starwars.lan.
25   IN  PTR yoda.starwars.lan.
26   IN  PTR c3p0.starwars.lan.
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
![DockerFile dns](/docs/img/salidas/5.1.png)
```bash
     nslookup skywalker.starwars.lan localhost
```
![DockerFile dns](/docs/img/salidas/EJ2222.png)
```bash
     nslookup starwars.lan localhost
```
![DockerFile dns](/docs/img/salidas/5.3.png)
```bash
     nslookup -q=mx starwars.lan localhost
```
![DockerFile dns](/docs/img/salidas/5.6.png)
```bash
     nslookup -q=ns starwars.lan localhost
```
![DockerFile dns](/docs/img/salidas/5.5.png)
```bash
     nslookup -q=soa starwars.lan localhost
```
![DockerFile dns](/docs/img/salidas/soa.png)
```bash
     nslookup -q=txt lenda.starwars.lan localhost
```
![DockerFile dns](/docs/img/salidas/5.8.png)
```bash
     nslookup 192.168.20.11 localhost
```
![DockerFile dns](/docs/img/salidas/last.png)

