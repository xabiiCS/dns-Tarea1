# Introducción

Neste documento detállanse os pasos necesarios para a configuración dun servidor DNS utilizando BIND9. Ao longo desta guía, explicarase como instalar e configurar o servidor, así como a creación de zonas de resolución directa e inversa. Ademais, proporcionarase exemplos de comandos e as súas saídas para verificar o correcto funcionamento do servidor DNS. Este traballo forma parte da entrega 1 da asignatura de Sistemas e Redes de Información (SRI).

---

## Requisitos Previos á tarea

### 1º Temos acceso a Internet?

#### Comprobación con comando:

![Acceso a Internet?](/docs/img-MD/pre1.png)

---

### 2º O Nome do host DNS é ***dartvader***?

#### Comprobación con comando:

![HOSTNAME](/docs/img-MD/pre2.png)

</br>

### 3º A súa direccion IPv4 é 192.168.20.10/24?

#### Comprobación con comando:

![Confirmar direccion IP](/docs/img-MD/pre3.png)

---

### 4º Instalar paquete dnsutils
#### Comprobación preinstalación dnsutils co DockerFIle e comando:

```bash
FROM debian:12

RUN apt update && apt install -y net-tools procps most iproute2  iputils-ping dnsutils

RUN apt install -y bind9 dnsutils rsyslog

RUN apt clean && rm -rf /var/lib/apt/lists/*

COPY entrypoint.sh /entrypoint.sh

RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
```


![DNSUTILS](/docs/img-MD/pre4.png)

---

### 5º Usar unha única interface de rede
#### Contido archivo compose:
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
---

# Respostas á Tarefa

---

### 1. Funciona o servidor BIND9?

#### Comando executado para a proba:
```bash
dig @localhost www.edu.xunta.gal
```

#### Saída del comando:
![Comando dig #1](/docs/img-MD/ej1.png)

---

### 2. Configuración do reenviador

#### Contenido do arquivo `/etc/bind/named.conf.options`:

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

![Comando dig #2](/docs/img-MD/ej2.png)

---

### 3. Creación dunha zoa primaria de resolución directa

#### Contido do arquivo da zoa:

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

#### Contido do arquivo `/etc/bind/named.conf.local`:

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

---

### 4. Creación dunha zoa de resolución inversa

#### Contido do arquivo da zoa:

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

---

### 5. Comprobación da resolución de rexistros

#### Comandos executados e as súas saídas:

```bash
nslookup darthvader.starwars.lan localhost
```

![DockerFile dns](/docs/img-MD/5.1.png)

---

```bash
nslookup skywalker.starwars.lan localhost
```

![DockerFile dns](/docs/img-MD/EJ2222.png)

---

```bash
     nslookup starwars.lan localhost
```

![DockerFile dns](/docs/img-MD/5.3.png)

---

```bash
     nslookup -q=mx starwars.lan localhost
```

![DockerFile dns](/docs/img-MD/5.6.png)

---

```bash
     nslookup -q=ns starwars.lan localhost
```

![DockerFile dns](/docs/img-MD/5.5.png)

---

```bash
     nslookup -q=soa starwars.lan localhost
```

![DockerFile dns](/docs/img-MD/soa.png)

---

```bash
     nslookup -q=txt lenda.starwars.lan localhost
```

![DockerFile dns](/docs/img-MD/5.8.png)

---

```bash
     nslookup 192.168.20.11 localhost
```

![DockerFile dns](/docs/img-MD/last.png)

