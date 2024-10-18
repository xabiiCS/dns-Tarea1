# Entrega

## 1. Configuración do servidor secundario

### 1.1. Ficheiros de configuración en **darthsidious**
- Capturas dos ficheiro de configuración local.
![DNS2-conf-local](/docs/2/img2/capturas/DNS2-conf-local.png)

### 1.2. Ficheiros de configuración en **darthvader**
- Captura do ficheiro de configuración local.
![DNS-conf-local](/docs/2/img2/capturas/DNS-conf-local.png)

---

## 2. Reinicio e transferencia de zona

### 2.1. Reinicio de **darthsidious**
- Captura do log de **darthsidious** mostrando a transferencia de zona.
- Captura do log de **darthvader** mostrando a transferencia de zona.

![logs](/docs/2/img2/logs/ej2-logs.png)

---

## 3. Engadir rexistro tipo A

### 3.1. Rexistro **Chewbacca**
- Captura do ficheiro de zona en **darthvader** coa entrada de **Chewbacca**.
![DNS2-zona-primaria](/docs/2/img2/capturas/DNS2-zona-primaria-montada.png)

- Captura do ficheiro de zona en **darthsidious** coa entrada de **Chewbacca**.
![DNS2-zona-inversa](/docs/2/img2/capturas/DNS2-zona-inversa-montada.png)

### 3.2. Comprobación de resolución
- Comprobación de que **darthsidious** pode resolver o nome **Chewbacca**.
![DNS2-zona-primaria](/docs/2/img2/capturas/llamada-chew-primaria.png)
![DNS2-zona-inversa](/docs/2/img2/capturas/llamada-chew-inversa.png)
---

## 4. Transferencias seguras

### 4.1. Configuración de chaves
- Captura dos ficheiros de configuración en **darthvader** e **darthsidious** para transferencias seguras.
![DNS2-segura](/docs/2/img2/capturas/DNS2-conf-local.png)
![DNS-segura](/docs/2/img2/capturas/DNS-conf-local.png)

### 4.2. Rexistro **r2d2**
- Captura de resolucion de zona en **darthsidious** coa entrada de **r2d2**.
![r2d2-ref-1](/docs/2/img2/capturas/llamada-r2d2-primaria.png)
![r2d2-ref-inv](/docs/2/img2/capturas/llamada-r2d2-inversa.png)
---

## 5. Conclusións

- Resumo dos pasos realizados.
- Problemas atopados e solucións aplicadas.
- Observacións adicionais.
