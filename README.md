# Vulnerabilidad IDOR

En este nuevo repositorio de la sección de **Bug bounty** se va a tratar la vulnerabilidad **IDOR**, desde la definición hasta cómo puede ser explotada.

<div>
  <img src="https://img.shields.io/badge/-Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white" />
  <img src="https://img.shields.io/badge/-Kali-5e8ca8?style=for-the-badge&logo=kalilinux&logoColor=white" />
  <img src="https://img.shields.io/badge/-Bash-4EAA25?style=for-the-badge&logo=gnubash&logoColor=white" />
  
</div>

## Introducción
Las siglas *IDOR* provienen de '*Insecure Direct Object Reference*' y ocurre cuando una aplicación expone un identificador interno (como un número de usuario, factura, archivo ...) y no comprueba si el usuario actual tiene permiso real para ver o modificar ese objeto.
Su funcionamiento implicaría que el sistema confía ciegamente en el identificador que envía el usuario (por ejemplo, en la URL ://sitio.com).Seguidamente el usuario cambia ese valor por otro (por ejemplo, id=1001) y si la aplicación no verifica la autorización en el servidor, le muestra los datos privados del usuario 1001 al usuario 1000.
## Explotación
