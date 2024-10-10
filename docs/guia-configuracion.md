
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
dig @localhost www.edu.xunta.es
```

#### Salida del comando:
```
root@darthvader:/# dig @localhost www.edu.xunta.es

; <<>> DiG 9.18.28-1~deb12u2-Debian <<>> @localhost www.edu.xunta.es
; (2 servers found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: SERVFAIL, id: 33029
;; flags: qr rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 96bf9b99ca9b5a08010000006707ab410ae4254820fc64e2 (good)
;; QUESTION SECTION:
;www.edu.xunta.es.		IN	A

;; Query time: 128 msec
;; SERVER: 127.0.0.1#53(localhost) (UDP)
;; WHEN: Thu Oct 10 10:24:01 UTC 2024
;; MSG SIZE  rcvd: 73

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

	// forwarders {
	//	8.8.8.8;
	// };

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
(Pega aquí la salida del comando)
```

### 3. Creación de una zona primaria de resolución directa

#### Contenido del archivo de zona:
```
(Pega aquí el contenido del archivo de zona)
```

#### Contenido del archivo `/etc/bind/named.conf.local`:
```
(Pega aquí el contenido del archivo)
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


