
# LAB-010 — RECONOCIMIENTO Y ENUMERACIÓN

**Proyecto:** FANTASMA — Pentesting & Red Team  
**Plataforma:** TryHackMe  
**Máquina:** Agent Sudo  
**IP del objetivo:** 10.67.163.128  
**Fecha:** 20/11/2026

## 1. Objetivo

Identificar los servicios expuestos, descubrir posibles puntos de entrada y recopilar información útil para las siguientes fases del laboratorio.

## 2. Escaneo de servicios

Comando utilizado:

    nmap -sV -Pn 10.67.163.128

Resultados:

| Puerto | Estado | Servicio | Versión |
|---|---|---|---|
| 21/tcp | open | FTP | vsftpd 3.0.3 |
| 22/tcp | open | SSH | OpenSSH 7.6p1 Ubuntu |
| 80/tcp | open | HTTP | Apache 2.4.29 |

### Interpretación

Se identificaron tres posibles superficies de ataque:

- FTP: autenticación y archivos compartidos.
- SSH: acceso remoto al sistema.
- HTTP: aplicación web con información sobre agentes.

## 3. Enumeración HTTP

Se consultó la página principal:

    curl http://10.67.163.128

La respuesta indicaba que debía utilizarse un nombre en clave mediante la cabecera HTTP User-Agent.

Se probó:

    curl -A 'C' -i http://10.67.163.128

El servidor respondió con una redirección HTTP 302 hacia:

    agent_C_attention.php

Para seguir la redirección:

    curl -L -A 'C' http://10.67.163.128

### Hallazgo

Se identificó el nombre del agente C:

    chris

La página también indicaba que su contraseña era débil.

## 4. Enumeración FTP

Se comprobó si el servidor permitía acceso anónimo:

    nmap --script ftp-anon -p 21 10.67.163.128

No se confirmó acceso anónimo.

Se identificó la necesidad de autenticarse con credenciales válidas.

## 5. Evaluación de credenciales

Se utilizó Hydra con el usuario descubierto y el diccionario RockYou:

    hydra -l chris -P /usr/share/wordlists/rockyou.txt -t 4 -f ftp://10.67.163.128

El ataque de diccionario permitió recuperar una contraseña válida para el usuario FTP.

Las credenciales se utilizaron únicamente dentro del entorno autorizado de TryHackMe.

## 6. Archivos identificados

Después de iniciar sesión mediante FTP se encontraron:

| Archivo | Descripción |
|---|---|
| To_agentJ.txt | Mensaje dirigido al agente J |
| cutie.png | Imagen con contenido ZIP incrustado |
| cute-alien.jpg | Imagen con datos ocultos mediante esteganografía |

El mensaje `To_agentJ.txt` indicaba que la contraseña de acceso del agente J estaba almacenada de alguna manera en una fotografía.

## 7. Herramientas utilizadas

- Nmap
- Curl
- Hydra
- FTP
- RockYou

## 8. Conclusiones

El reconocimiento permitió identificar servicios expuestos y obtener información relevante mediante la manipulación de la cabecera User-Agent.

La combinación de enumeración web y evaluación de contraseñas permitió acceder al servicio FTP y descubrir archivos importantes para continuar el laboratorio.

**Resultado:** Reconocimiento completado y punto de entrada identificado.

---

**FANTASMA — LAB-010 AGENT SUDO**
