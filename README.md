# tplink-firmware-analysis
En este repositorio se realiazrá un análisis del firmware de un router TP-Link, en la página web del fabricante se puede descargar el archivo .bin.

<img width="932" height="270" alt="image" src="https://github.com/user-attachments/assets/80555028-24f0-43d3-94c1-561f44307323" />

La mejor herramienta para este tipo de tareas es Kali Linux que ya trae todas las herramientas preinstaladas por defecto,
si no quieres formatear tu disco duro puedes descargarte la máquina virtual ya preparada para ejecutarla en Oracle Virtual Box

<img width="1919" height="1034" alt="image" src="https://github.com/user-attachments/assets/fcc0baf0-c0d9-433a-b1a5-8d55b29c1dde" />

Abrimos el terminal en el directorio del archivo .bin seleccionando en "Open terminal here" o puedes usar el comando "cd"

<img width="949" height="954" alt="image" src="https://github.com/user-attachments/assets/7cec9e0b-c00e-403d-b016-292bb871e3f5" />

En primer lugar hay que averiguar con qué archivo se está tratando, por ello en el terminal usaremos :
file tplink.bin

<img width="947" height="153" alt="image" src="https://github.com/user-attachments/assets/6dee2b7f-109f-40e2-b7e7-d00287e9b5b6" />

Esto nos confirma que estamos tratando con un firmware por lo que se posible seguir adelante para analizar, extraer información y sacar nuestras conclusiones, para ello, binwalk será una herramienta muy útil

<img width="902" height="271" alt="image" src="https://github.com/user-attachments/assets/4da5dba5-0dd9-4f38-85c0-837c04a7fbaf" />

Podemos observar dos archivos comprimidos LZMA y un sistema de archivos Squashfs que es básicamente como está distribuido Linux.

<img width="435" height="221" alt="image" src="https://github.com/user-attachments/assets/2e502b9e-ec81-4722-bae6-cbe46bad72d5" />

Lo que nos interesa es donde pone decimal y hexadecimal, saber el valor en decimal nos permitrá extraer sólo los archivos que nos interesa.

En el caso de squashfs filesystem, comienza en 1180160, y en el caso del LZMA data comienza en el 142344, Con esta información podríamos usar "dd".

dd if=tplink.bin bs=1 skip=1180160 of=rootfs.sqfs

<img width="553" height="117" alt="image" src="https://github.com/user-attachments/assets/512f0bbf-3a68-47f6-9cdd-6195009210d2" />

Una vez extraido solo esa "porción" de archivo obtendremos el sistema de archivos que está comprimido también
<img width="940" height="76" alt="image" src="https://github.com/user-attachments/assets/a5f00481-f592-4411-9b84-f5a624f720b6" />

**Extrayendo el sistema de archivos**
Para ver como está distrubuido el sistema de archivos usamos este comando
unsquashfs -l rootfs.sqfs 

<img width="602" height="633" alt="image" src="https://github.com/user-attachments/assets/81b0d995-9326-4850-bee1-0c743e94a211" />

Podemos determinar que el sistema de archivos es igual que el de linux
sudo unsquashfs rootfs.sqfs

<img width="954" height="245" alt="image" src="https://github.com/user-attachments/assets/1b775a2a-1c2d-4a6b-b031-09836fdec4a2" />

<img width="488" height="119" alt="image" src="https://github.com/user-attachments/assets/b422a521-4cbf-4d7d-bad4-996ea160dced" />

