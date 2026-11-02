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
