# 1.2 Instalación de Zonas Maestras Primarias en Windows Server

**Requisitos de finalización**  
**Cierre:** lunes, 28 de octubre de 2024, 23:59

## Objetivo

El objetivo de esta tarea es instalar el servidor DNS en una máquina Windows 2019 Server.

## Instrucciones

Partiremos de una máquina virtual con Windows 2019 con un único adaptador. Antes de comenzar, comprueba que desde ella se puede acceder a Internet. El nombre de la máquina debe ser "lukeskywalker" y la dirección IP 192.168.20.101/24.

Deberás entregar un único documento de Google Drive donde pegues el resultado de cada una de las tareas.

1. **Instalación del servidor DNS:**
    - Instala el servidor DNS en el equipo lukeskywalker.
    - Comprueba que ya funciona como servidor DNS caché pegando en el documento de entrega la salida de este comando:
      ```sh
      nslookup -norecurse www.starwars.com localhost
      ```

2. **Configuración del reenviador:**
    - Configura el servidor DNS para que emplee como reenviador 8.8.8.8.
    - Pega en el documento de entrega la captura de pantalla de la configuración del reenviador y la salida de este comando:
      ```sh
      nslookup -recurse www.starwars.com localhost
      ```

3. **Instalación de una zona primaria de resolución directa:**
    - Crea una zona primaria llamada "academia.jedi" y añade los siguientes registros de recursos:
        - Tipo A: lukeskywalker con IP 192.168.20.101
        - Tipo A: benskywalker con IP 192.168.20.102
        - Tipo A: obiwankenobi con IP 192.168.56.152 y 192.168.56.153
        - Tipo A: hansolo con IP 192.168.56.105
        - Tipo A: leia con IP 192.168.56.106
        - Tipo A: anakinsolo con IP 192.168.56.106
        - Tipo CNAME: mestrejedi a obiwankenobi
        - Tipo MX: con prioridad 10 sobre el equipo hansolo
        - Tipo TXT: "thon" con "un Jedi debe sentir a tensión entre os dous lados da Forza"
        - Tipo NS: con benskywalker
    - Pega en el documento de entrega la captura de los registros creados.

4. **Instalación de una zona de resolución inversa:**
    - Crea una zona de resolución inversa relacionada con la dirección del equipo lukeskywalker.
    - Añade registros PTR para los registros tipo A del ejercicio anterior.
    - Pega en el documento de entrega la captura de los registros de la zona.

5. **Comprobación de resolución de registros:**
    - Comprueba que puedes resolver los distintos registros de recursos. Pega en el documento de entrega la salida de los siguientes comandos:
      ```sh
      nslookup lukeskywalker.academia.jedi localhost
      nslookup hansolo.academia.jedi localhost
      nslookup leia.academia.jedi localhost
      nslookup anakinsolo.academia.jedi localhost
      nslookup academia.jedi localhost
      nslookup -q=mx academia.jedi localhost
      nslookup -q=ns academia.jedi localhost
      nslookup -q=soa academia.jedi localhost
      nslookup -q=txt thon.academia.jedi localhost
      nslookup 192.168.56.101 localhost
      ```