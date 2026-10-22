# Vulnerabilidad IDOR

En este nuevo repositorio de la sección de **Bug bounty** se va a tratar la vulnerabilidad **IDOR**, desde la definición hasta cómo puede ser explotada.

<div>
  <img src="https://img.shields.io/badge/-Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white" />
  <img src="https://img.shields.io/badge/-Kali-5e8ca8?style=for-the-badge&logo=kalilinux&logoColor=white" />
  <img src="https://img.shields.io/badge/-Bash-4EAA25?style=for-the-badge&logo=gnubash&logoColor=white" />
  
</div>

## Introducción
Las siglas *IDOR* provienen de '*Insecure Direct Object Reference*' y ocurre cuando una aplicación expone un identificador interno (como un número de usuario, factura, archivo ...) y no comprueba si el usuario actual tiene permiso real para ver o modificar ese objeto.

Su funcionamiento implicaría que el sistema confía ciegamente en el identificador que envía el usuario (por ejemplo, id=1000 en la URL ://sitio.com). Seguidamente el usuario cambia ese valor por otro (por ejemplo, id=1001) y si la aplicación no verifica la autorización en el servidor, le muestra los datos privados del usuario 1001 al usuario 1000.

Para llevar a cabo este ejercicio se ha utilizado la plataforma *[Docker labs](https://dockerlabs.es/)*, donde se encuentra la máquina virtual que utilizaremos '*Loopwise*'. Se descarga el *zip* en la propia *Kali* y extraemos el fichero *autodeploy.sh* y el archivo *tar*, que será la máquina con la que practicar. Para desplegar el laboratorio y obtener la direccion IP víctima sobre la que trabajar, se escribe el siguiente comando en la terminal:

<code>sudo bash autodeploy.sh loopwise.tar</code>

Como último paso se deben añadir la IP y la dirección en '/etc/hosts', cómo se ha realizado en anteriores publicaciones. Una vez realizadas estas acciones, se procede a explotar la vulnerabilidad.

## Explotación
Sin embargo, 
