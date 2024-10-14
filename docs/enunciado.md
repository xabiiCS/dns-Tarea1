# 1.1 Instalación de Zonas Maestras Primarias

**Requisitos de finalización**  

- Apertura: Miércoles, 28 de septiembre de 2022, 00:00  
- Cierre: Lunes, 14 de octubre de 2024, 23:59  

## Objetivo de la tarea

El objetivo de esta tarea es instalar el servidor **DNS BIND9** en una máquina virtual con **Debian 12**. Podes descargar un esqueleto de este [repositorio en GitHub](https://github.com/brunosct/bind9skel).

**Nota:** Partiremos del escenario facilitado. Antes de comenzar, comprueba que desde la máquina se puede acceder a Internet. El nombre de la máquina debe ser **darthvader** y su dirección IP **192.168.20.10/24**.

Puedes usar contenedores con **Debian 12** y un único interfaz de red.

### Requisitos adicionales ✅

Es posible que necesites instalar el paquete `dnsutils`.

### Instrucciones

1. **Instalar el servidor BIND9** ✅
   - Instala el servidor **BIND9** en el equipo `darthvader`.
   - Comprobar que ya funciona como servidor DNS caché pegando en el documento de entrega la salida del siguiente comando:

     ```bash
     dig @localhost www.edu.xunta.gal
     ```

2. **Configurar un reenviador** ✅
   - Configura el servidor **BIND9** para que use como reenviador el **DNS 8.8.8.8**.
   - Pega en el documento de entrega el contenido del archivo `/etc/bind/named.conf.options` y la salida del siguiente comando:

     ```bash

     dig @localhost www.mecd.gob.es
     ```

3. **Crear una zona primaria de resolución directa**
   - Instala una zona primaria de resolución directa llamada `starwars.lan` y añade los siguientes registros de recursos:
     - **Tipo A:**  
       - `darthvader` con IP `192.168.20.10`
       - `skywalker` con IPs `192.168.20.101` y `192.168.20.111`
       - `luke` con IP `192.168.20.22`
       - `darthsidious` con IP `192.168.20.11`
       - `yoda` con IPs `192.168.20.24` y `192.168.20.25`
       - `c3p0` con IP `192.168.20.26`
     - **Tipo CNAME:** `palpatine` apuntando a `darthsidious`
     - **Tipo MX:** Con prioridad 10 sobre el equipo `c3p0`
     - **Tipo TXT:** `"lenda"` con el valor `"Que a forza te acompanhe"`
     - **Tipo NS:** `darthsidious`
   - Pega en el documento de entrega el contenido del archivo de zona y el archivo `/etc/bind/named.conf.local`.

4. **Crear una zona de resolución inversa**
   - Crea una zona de resolución inversa correspondiente a la IP del equipo `darthvader` y añade registros **PTR** para los registros **A** del ejercicio anterior.
   - Pega en el documento de entrega el contenido del archivo de zona y el archivo `/etc/bind/named.conf.local`.

5. **Comprobar la resolución de registros**
   - Comprobar que puedes resolver los diferentes registros de recursos. Pega en el documento de entrega la salida de los siguientes comandos:

     ```bash

     nslookup darthvader.starwars.lan localhost
     nslookup skywalker.starwars.lan localhost
     nslookup starwars.lan localhost
     nslookup -q=mx starwars.lan localhost
     nslookup -q=ns starwars.lan localhost
     nslookup -q=soa starwars.lan localhost
     nslookup -q=txt lenda.starwars.lan localhost
     nslookup 192.168.20.11 localhost
     ```
