# 1.1 Instalación de Zonas Maestras Primarias

**Requisitos de finalización**  

- Apertura: Mércores, 28 de setembro de 2022, 00:00  
- Peche: Luns, 14 de outubro de 2024, 23:59  

## Obxectivo da tarefa

O obxectivo desta tarefa é instalar o servidor **DNS BIND9** nunha máquina virtual con **Debian 12**. Podes descargar un esqueleto deste [repositorio en GitHub](https://github.com/brunosct/bind9skel).

**Nota:** Partiremos do escenario facilitado. Antes de comezar, comproba que desde a máquina se pode acceder a Internet. O nome da máquina debe ser **darthvader** e a súa dirección IP **192.168.20.10/24**.

Podes usar contedores con **Debian 12** e unha única interface de rede.

### Requisitos adicionais ✅

É posible que necesites instalar o paquete `dnsutils`.

### Instrucións

1. **Instalar o servidor BIND9** ✅
  - Instala o servidor **BIND9** no equipo `darthvader`.
  - Comprobar que xa funciona como servidor DNS caché pegando no documento de entrega a saída do seguinte comando:

    ```bash
    dig @localhost www.edu.xunta.gal
    ```

2. **Configurar un reenviador** ✅
  - Configura o servidor **BIND9** para que use como reenviador o **DNS 8.8.8.8**.
  - Pega no documento de entrega o contido do arquivo `/etc/bind/named.conf.options` e a saída do seguinte comando:

    ```bash
    dig @localhost www.mecd.gob.es
    ```

3. **Crear unha zona primaria de resolución directa**
  - Instala unha zona primaria de resolución directa chamada `starwars.lan` e engade os seguintes rexistros de recursos:
    - **Tipo A:**  
      - `darthvader` con IP `192.168.20.10`
      - `skywalker` con IPs `192.168.20.101` e `192.168.20.111`
      - `luke` con IP `192.168.20.22`
      - `darthsidious` con IP `192.168.20.11`
      - `yoda` con IPs `192.168.20.24` e `192.168.20.25`
      - `c3p0` con IP `192.168.20.26`
    - **Tipo CNAME:** `palpatine` apuntando a `darthsidious`
    - **Tipo MX:** Con prioridade 10 sobre o equipo `c3p0`
    - **Tipo TXT:** `"lenda"` co valor `"Que a forza te acompañe"`
    - **Tipo NS:** `darthsidious`
  - Pega no documento de entrega o contido do arquivo de zona e o arquivo `/etc/bind/named.conf.local`.

4. **Crear unha zona de resolución inversa**
  - Crea unha zona de resolución inversa correspondente á IP do equipo `darthvader` e engade rexistros **PTR** para os rexistros **A** do exercicio anterior.
  - Pega no documento de entrega o contido do arquivo de zona e o arquivo `/etc/bind/named.conf.local`.

5. **Comprobar a resolución de rexistros**
  - Comprobar que podes resolver os diferentes rexistros de recursos. Pega no documento de entrega a saída dos seguintes comandos:

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