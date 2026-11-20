
# INFORME TÉCNICO — LAB-010 AGENT SUDO

**Proyecto:** FANTASMA — Pentesting & Red Team  
**Plataforma:** TryHackMe  
**Máquina:** Agent Sudo  
**Dificultad:** Fácil  
**Fecha:** 20/11/2026  
**Estado:** Completado  
**Alcance:** Entorno de laboratorio autorizado

---

## 1. Resumen ejecutivo

Se realizó una evaluación de seguridad sobre la máquina Agent Sudo de TryHackMe.

Durante el ejercicio se identificaron debilidades relacionadas con contraseñas, exposición de información y configuración de privilegios.

Se consiguió acceso inicial al sistema como un usuario estándar y posteriormente se obtuvo acceso root mediante CVE-2019-14287.

**Resultado:** Compromiso completo de la máquina del laboratorio.

## 2. Alcance

Objetivo evaluado: `10.67.163.128`

Servicios identificados:

| Puerto | Servicio | Versión |
|---|---|---|
| 21 | FTP | vsftpd 3.0.3 |
| 22 | SSH | OpenSSH 7.6p1 |
| 80 | HTTP | Apache 2.4.29 |

La dirección IP fue asignada temporalmente por TryHackMe.

## 3. Metodología

El ejercicio se desarrolló mediante las siguientes fases:

1. Reconocimiento y enumeración de servicios.
2. Enumeración HTTP mediante User-Agent.
3. Evaluación de credenciales FTP.
4. Análisis de archivos y extracción de ZIP cifrado.
5. Recuperación de contraseñas mediante diccionario.
6. Análisis de esteganografía.
7. Acceso SSH.
8. Enumeración de privilegios.
9. Escalada de privilegios a root.
10. Recuperación de banderas y verificación de resultados.

## 4. Hallazgos de seguridad

### H-01 — Contraseña FTP débil

**Severidad:** Alta

Se identificó un usuario FTP cuya contraseña pudo recuperarse mediante un ataque de diccionario utilizando Hydra y RockYou.

**Impacto:** Acceso no autorizado a archivos disponibles para la cuenta comprometida.

**Recomendaciones:**
- Aplicar contraseñas robustas y únicas.
- Limitar intentos de autenticación.
- Monitorizar accesos fallidos.
- Evitar FTP sin protección del canal cuando se transmiten credenciales.

### H-02 — Información sensible oculta en archivos

**Severidad:** Alta

Se identificaron datos ocultos en archivos de imagen y un archivo ZIP cifrado.

El análisis permitió recuperar información utilizada posteriormente para acceder al sistema mediante SSH.

**Impacto:** Exposición de credenciales y posibilidad de movimiento desde el acceso FTP hacia SSH.

**Recomendaciones:**
- No almacenar credenciales en imágenes o documentos accesibles.
- Utilizar sistemas de gestión de secretos.
- Restringir permisos y acceso a archivos sensibles.

### H-03 — Configuración vulnerable de sudo

**Severidad:** Crítica

**Identificador:** CVE-2019-14287

**Versión observada:** sudo 1.8.21p2

**Regla identificada:**

    (ALL, !root) /bin/bash

La configuración permitía ejecutar Bash como otros usuarios, excluyendo explícitamente a root.

Sin embargo, la versión vulnerable de sudo permitió eludir la restricción mediante un identificador de usuario especial.

**Prueba realizada:**

    sudo -u#-1 /bin/bash

**Resultado de verificación:**

    uid=0(root) gid=1000(james) groups=1000(james)

**Impacto:** Escalada de privilegios desde una cuenta estándar hasta root.

**Recomendaciones:**
- Actualizar sudo a una versión corregida.
- Revisar las reglas sudoers.
- Aplicar el principio de mínimo privilegio.
- Evitar permitir intérpretes de comandos cuando no sean necesarios.

## 5. Evidencias verificadas

| Evidencia | Resultado |
|---|---|
| Enumeración de puertos | FTP, SSH y HTTP |
| Enumeración web | Identificación del agente C |
| Acceso FTP | Autenticación conseguida |
| Archivo ZIP | Contraseña recuperada |
| Esteganografía | Documento oculto recuperado |
| Acceso SSH | Sesión como james |
| Enumeración sudo | Regla vulnerable identificada |
| Escalada | UID 0 confirmado |
| User flag | Recuperada |
| Root flag | Recuperada |

Las credenciales, banderas y evidencias sensibles deben mantenerse fuera de los archivos públicos cuando no sean necesarias para explicar el hallazgo.

## 6. Conclusión

El laboratorio permitió demostrar una cadena de compromiso que comenzó con la enumeración de servicios y terminó con acceso administrativo completo.

La lección principal es que varias debilidades pueden combinarse para producir un impacto mayor: una contraseña débil, información sensible expuesta y una configuración vulnerable de sudo.

El ejercicio se completó en un entorno autorizado de TryHackMe con fines de aprendizaje y documentación profesional.

---

**FANTASMA — Pentesting & Red Team**  
**LAB-010 — AGENT SUDO**
