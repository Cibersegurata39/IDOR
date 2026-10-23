# Vulnerabilidad IDOR

En este nuevo repositorio de la sección de **Bug bounty** se va a tratar la vulnerabilidad **IDOR**, desde la definición hasta cómo puede ser explotada.

<div>
  <img src="https://img.shields.io/badge/-Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white" />
  <img src="https://img.shields.io/badge/-Kali-5e8ca8?style=for-the-badge&logo=kalilinux&logoColor=white" />
  <img src="https://img.shields.io/badge/-Bash-4EAA25?style=for-the-badge&logo=gnubash&logoColor=white" />
  <img src="https://img.shields.io/badge/-Nmap-6933FF?style=for-the-badge&logo=nmap&logoColor=white" />
  <img src="https://img.shields.io/badge/-Dirbuster-005571?style=for-the-badge&logo=dirbuster&logoColor=white" />
  <img src="https://img.shields.io/badge/-Curl-0735511?style=for-the-badge&logo=curl&logoColor=white" />  
</div>

## Introducción
Las siglas *IDOR* provienen de '*Insecure Direct Object Reference*' y ocurre cuando una aplicación expone un identificador interno (como un número de usuario, factura, archivo ...) y no comprueba si el usuario actual tiene permiso real para ver o modificar ese objeto.

Su funcionamiento implicaría que el sistema confía ciegamente en el identificador que envía el usuario (por ejemplo, id=1000 en la URL ://sitio.com). Seguidamente el usuario cambia ese valor por otro (por ejemplo, id=1001) y si la aplicación no verifica la autorización en el servidor, le muestra los datos privados del usuario 1001 al usuario 1000.

Para llevar a cabo este ejercicio se ha utilizado la plataforma *[Docker labs](https://dockerlabs.es/)*, donde se encuentra la máquina virtual que utilizaremos '*Loopwise*'. Se descarga el *zip* en la propia *Kali* y extraemos el fichero *autodeploy.sh* y el archivo *tar*, que será la máquina con la que practicar. Para desplegar el laboratorio y obtener la direccion IP víctima sobre la que trabajar, se escribe el siguiente comando en la terminal:

<code>sudo bash autodeploy.sh loopwise.tar</code>

Como último paso se deben añadir la IP y la dirección en '/etc/hosts', cómo se ha realizado en anteriores publicaciones. Una vez realizadas estas acciones, se procede a explotar la vulnerabilidad.

## Explotación
Sin embargo, al dirigirnos a la IP correspondiente, no se consigue alcanzar. Por ello se realiza un **Nmap** para encontrar posibles puertos a los que conectarnos (-p- indica que se miren todos y cada uno de los puertos). Se evita hacer ping (-Pn) y resolver nombres DNS (-n) para agilizar el escaneo. Para conocer las versiones que corren en cada puerto se indica el parámetro -sV y para añadirle velocidad al proceso se añade -T4.

<code>nmap -Pn -n -sV -p- -T4 172.17.0.2</code>

Como resultado se ha descubierto el puerto 4000 con el servicio *http*, a través de este nos deberemos conectar para acceder a la página.

<img width="692" height="198" alt="Captura de pantalla 2026-10-21 185214" src="https://github.com/user-attachments/assets/bcfca483-a706-455e-a185-8c2c0988270d" />

La *web* en cuestión se encarga de crear automatismos (programador de tareas por tiempo)  para los usuarios registrados, es una especie de *cron* de *Linux*. Primero se va a realizar una enumeración *web* para ver si podemos obtener información útil para explotar la vulnerabilidad. El programa encargado de facilitarnos los directorios y archivos es *Dirbuster*, al cual le pasamos la *URL* víctima (http://172.17.0.2:4000) y la lista con la que hará la búsqueda (/usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt). Se han obtenido resultados provechosos al restringir la búsqueda a archivos *html* y *php*.

<img width="757" height="473" alt="Captura de pantalla 2026-10-22 005337" src="https://github.com/user-attachments/assets/6d89cd6e-3537-4f8a-9fca-0e13d4dd7f27" />

De entre los archivos descubiertos, destacan como especialmente útiles dos. Por un lado '/docs.html', el cual contiene información muy valiosa de cómo funciona la *API*. Y por otro lado, '/explore.html' proporciona información sobre los usuarios de la aplicación. Entre todos los datos que nos ofrece, nos interesa la ID de usuario pues es el que nos permitirá llevar a cabo el *IDOR*.

<img width="755" height="477" alt="Captura de pantalla 2026-10-22 005221" src="https://github.com/user-attachments/assets/9deda17f-1eb6-420e-ab2b-195ff7008cc2" />

Investigando la documentación de la *API*, se encuentra la sgieunte tabla que explica las distintas acciones que se pueden hacer sobre las automatizaciones de los usuarios.

|Accíon|Directorio|Explicación|
|--|--|--|
|GET | /api/v1/automations | List automations for X-User-Id|
|POST|	/api/v1/automations|	Create an automation|
|GET|	/api/v1/automations/{id}|	Read one automation|
|PUT|	/api/v1/automations/{id}|	Update fields|
|DELETE|	/api/v1/automations/{id}|	Delete an automation|
|GET|	/api/v1/automations/{id}/executions|	Run history|
