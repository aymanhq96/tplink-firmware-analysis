# Análisis y reverse engineering de firmware TP-Link

Este repositorio documenta el proceso de ingeniería inversa y análisis de seguridad del firmware de un router TP-Link. El objetivo es inspeccionar el binario original, extraer el sistema de archivos y auditar posibles vulnerabilidades en la gestión de credenciales.

El archivo `.bin` original se puede descargar directamente desde el portal de soporte del fabricante.

<img width="932" height="270" alt="image" src="https://github.com/user-attachments/assets/80555028-24f0-43d3-94c1-561f44307323" />

## Entorno de trabajo

Para este tipo de auditorías se recomienda utilizar **Kali Linux**, ya que incorpora las herramientas de análisis binario y forense preinstaladas por defecto. Si no deseas instalarlo como sistema operativo nativo, puedes desplegar la máquina virtual oficial en Oracle VirtualBox.

<img width="1919" height="1034" alt="image" src="https://github.com/user-attachments/assets/fcc0baf0-c0d9-433a-b1a5-8d55b29c1dde" />

## Fase 1: Identificación y análisis del binario

Abrimos el terminal en el directorio donde se encuentra el archivo `.bin` (puedes usar "Open terminal here" o navegar con `cd`) y ejecutamos la utilidad `file` para comprobar el tipo de archivo:

```bash
file tplink.bin
```
<img width="947" height="153" alt="image" src="https://github.com/user-attachments/assets/6dee2b7f-109f-40e2-b7e7-d00287e9b5b6" />

Al confirmar que se trata de un archivo de datos (firmware), procedemos a analizar su estructura interna y extraer sus componentes con `binwalk`.

```bash
binwalk tplink.bin
```
<img width="902" height="271" alt="image" src="https://github.com/user-attachments/assets/4da5dba5-0dd9-4f38-85c0-837c04a7fbaf" />

La salida revela dos bloques de datos comprimidos en LZMA y un sistema de archivos **Squashfs**, el estándar utilizado habitualmente en distribuciones Linux embebidas.

<img width="435" height="221" alt="image" src="https://github.com/user-attachments/assets/2e502b9e-ec81-4722-bae6-cbe46bad72d5" />

## Fase 2: Extracción manual del sistema de archivos

Para extraer una partición específica de forma manual, necesitamos conocer su offset en base decimal.
- **LZMA data:** comienza en el offset `142344`
- **Squashfs filesystem:** comienza en el offset `1180160`

Utilizamos el comando `dd` para volcar un bloque de datos que empiece exactamente en el offset del sistema de archivos Squashfs:

```bash
dd if=tplink.bin bs=1 skip=1180160 of=rootfs.sqfs
```
<img width="553" height="117" alt="image" src="https://github.com/user-attachments/assets/512f0bbf-3a68-47f6-9cdd-6195009210d2" />

Esto nos genera el archivo `rootfs.sqfs`, el cual contiene la porción correspondiente al sistema de archivos (aún comprimido).

<img width="940" height="76" alt="image" src="https://github.com/user-attachments/assets/a5f00481-f592-4411-9b84-f5a624f720b6" />

## Fase 3: Desempaquetado y auditoría

Para ver cómo está distribuida la jerarquía de directorios antes de extraerlos, ejecutamos:

```bash
unsquashfs -l rootfs.sqfs
```
<img width="602" height="633" alt="image" src="https://github.com/user-attachments/assets/81b0d995-9326-4850-bee1-0c743e94a211" />

Comprobamos que mantiene la estructura estándar de Linux. Procedemos a desempaquetarlo por completo:

```bash
sudo unsquashfs rootfs.sqfs
```
<img width="954" height="245" alt="image" src="https://github.com/user-attachments/assets/1b775a2a-1c2d-4a6b-b031-09836fdec4a2" />

<img width="488" height="119" alt="image" src="https://github.com/user-attachments/assets/b422a521-4cbf-4d7d-bad4-996ea160dced" />

<img width="534" height="345" alt="image" src="https://github.com/user-attachments/assets/8d77b916-6164-4426-9d22-fad3dd94cb04" />

### Análisis de culnerabilidades en credenciales

Uno de los principales vectores de ataque en dispositivos embebidos es la gestión de credenciales. Accedemos al directorio crítico `squashfs-root/etc` en busca de información de usuarios y contraseñas.

<img width="677" height="254" alt="image" src="https://github.com/user-attachments/assets/0b5dc2bf-2bae-450e-a71c-ea3a9cb64f5f" />

Al inspeccionar el archivo que contiene las credenciales del sistema, identificamos varias brechas de seguridad críticas:

1. **Algoritmo de cifrado obsoleto:** La presencia de `$1$` en la primera columna indica que el sistema está utilizando **MD5** para el hashing de contraseñas. Este algoritmo se considera criptográficamente roto y es altamente vulnerable a ataques de fuerza bruta y colisiones.
2. **Reutilización de claves:** Los usuarios `root` y `admin` comparten exactamente la misma cadena hash. Esto significa que comparten la contraseña maestra, violando el principio de segmentación y mínimo privilegio.


