---
layout: single
title: Agregando HDD vía USB a Raspberry Pi 4
excerpt: "Se detalla cómo agregar un HDD a la Raspberry Pi 4 para utilizarlo como almacenamiento en aplicaciones que se instalarán posteriormente como GitLab, OwnCloud, etc... y evitar hacer uso de las nubes de terceros."
date: 2023-01-25
classes: wide
header:
  teaser: /assets/images/agregar-hdd-via-usb-a-raspberrypi-4/banner_PostHDD_Rpi4.png
  teaser_home_page: true
categories:
  - RaspberryPi
  - HDD
  - USB Storage
tags:  
  - usb
  - storage
---
<span align="center" style="color:red;">
![banner_PostHDD_Rpi4.png](/assets/images/agregar-hdd-via-usb-a-raspberrypi-4/banner_PostHDD_Rpi4.png)
</span>
<br><br>

En esta entrada, se detalla cómo agregar y configurar un HDD a la Raspberry Pi 4.
<br>

El motivo de expandir la memoria del dispositivo, será el utilizarlo como almacenamiento en aplicaciones que se instalarán posteriormente como GitLab y OwnCloud para evitar en lo posible hacer uso de las nubes de terceros.
<br>

<span align="center" style="color:red;">
![1ae69a795627de61667698b9705c89d5.png](/assets/images/agregar-hdd-via-usb-a-raspberrypi-4/1ae69a795627de61667698b9705c89d5.png)
</span>

<br>

### Materiales Empleados:
- Raspberry Pi 4 8Gb.
- Disco HDD 2.5'' SATA 1TB.
- Adaptador SATA a USB3.0.
- **El puerto USB3.0 de la Raspberry disponible**.
- Diseño 3D de la Caja para Raspberry Pi [By "Chordless"]: <a href="https://www.thingiverse.com/thing:4399405" target="_blank">The Stack - Modular Raspberry Pi Case</a>
<br><br><br><br>



# Requisitos previos
  - Conectar el adaptador SATA-USB3.0 al Disco que hayan adquirido y conectarlo al USB3.0 de la Raspberry Pi.
  - Iniciar sesión en la Raspberry Pi para comenzar.
<br><br><br>

# Instalar Gparted

Para configurar el nuevo Disco se utilizará "**gparted**", si no lo tenemos instalado procedemos a su instalación.<br>

- Para instalar "**gparted**" en la **Raspberry Pi** ejecutamos en un terminal:<br>
```sudo apt install gparted```<br>
<span align="center" style="color:red;">
![5e8c0b40f2455ee5ec3a33aa0a2cfae7.png](/assets/images/agregar-hdd-via-usb-a-raspberrypi-4/5e8c0b40f2455ee5ec3a33aa0a2cfae7.png)
</span>

<br>

- Ejecutamos "**gparted**" con privilegios de superusuario:<br>
```sudo gparted```<br>
<span align="center" style="color:red;">
![5379dc783e89f53fae1a63f0dc2b2e81.png](/assets/images/agregar-hdd-via-usb-a-raspberrypi-4/5379dc783e89f53fae1a63f0dc2b2e81.png)
</span><br>
*En caso de obtener un error como el que se muestra en la imagen, no hay de qué preocuparse, simplemente no tenemos permisos para ejecutarlo. Realizamos el siguiente paso para arreglarlo.*
<br><br>

- **Si nos hemos encontrado con el error**, damos permisos para que cualquier usuario pueda ejecutar aplicaciones con interfaz gráfica desde el terminal (durará hasta el reinicio), en el terminal ejecutamos:<br>
```xhost +```<br>
<span align="center" style="color:red;">
![dce6b6030334176947d5665dcc3b9e07.png](/assets/images/agregar-hdd-via-usb-a-raspberrypi-4/dce6b6030334176947d5665dcc3b9e07.png)
</span>

<br>

- Volvemos a ejecutar "**gparted**" con privilegios para comenzar a configurar nuestro nuevo disco.<br>
```sudo gparted```<br>
<span align="center" style="color:red;">
![0613ec3ed2b0145956a8b9d7b6ebc0e6.png](/assets/images/agregar-hdd-via-usb-a-raspberrypi-4/0613ec3ed2b0145956a8b9d7b6ebc0e6.png)
</span>

<br>

- Se abrirá "**gparted**" y podremos ver las particiones de alguna unidad.<br>
Para seleccionar la unidad correcta, pulsamos en la flecha que se muestra en la imagen y se desplegarán la unidades disponibles.<br>
<span align="center" style="color:red;">
![769bb753a9cba6e89f117f288cd3f308.png](/assets/images/agregar-hdd-via-usb-a-raspberrypi-4/769bb753a9cba6e89f117f288cd3f308.png)
</span>

<br>

- Seleccionamos la unidad correspondiente.<br>
<span align="center" style="color:red;">
![0612d1505351c2bfad36da1693d87be5.png](/assets/images/agregar-hdd-via-usb-a-raspberrypi-4/0612d1505351c2bfad36da1693d87be5.png)
</span>

<br>

En caso de ser un HDD **nuevo**, se debe crear una tabla de particiones en la unidad.<br>

- Para crear la tabla de particiones, nos dirigimos a "**Dispositivo**" y seleccionamos "**Crear tabla de particiones...**"<br>
<span align="center" style="color:red;">
![695fe395aa9318959df643b59a12b0d3.png](/assets/images/agregar-hdd-via-usb-a-raspberrypi-4/695fe395aa9318959df643b59a12b0d3.png)
</span>

<br>

- Se nos desplegará una ventana para crear la tabla de particiones.<br>
Seleccionamos como tipo de tabla de particiones "**msdos**" y le damos a "**Aplicar**".
<span align="center" style="color:red;">
![6657cbef26ce331f4e98f0a942f93a5f.png](/assets/images/agregar-hdd-via-usb-a-raspberrypi-4/6657cbef26ce331f4e98f0a942f93a5f.png)
</span>

***Después de esperar un poco, la tabla de particiones se habrá creado y ya nos permitirá crear particiones.***
<br><br>

- Para crear la partición, pulsamos con el botón derecho del ratón sobre el volumen sin asignar y seleccionamos "**Nueva**"<br>
<span align="center" style="color:red;">
![4aa19bd1196fc4cda297b164bb806dbe.png](/assets/images/agregar-hdd-via-usb-a-raspberrypi-4/4aa19bd1196fc4cda297b164bb806dbe.png)
</span>

<br>

- En mi caso utilizaré la unidad entera.<br>
<span align="center" style="color:red;">
![2108c9f54a1358085f42326206790a1f.png](/assets/images/agregar-hdd-via-usb-a-raspberrypi-4/2108c9f54a1358085f42326206790a1f.png)
</span>

<br>

<br>

- Se observa cómo ha cambiado el color con el que se mostraba en "**gparted**" y el nombre de la partición.<br>
Para comenzar a ejecutar los cambios, debemos pulsar el "**Check**" como se muestra en la imagen:<br>
<span align="center" style="color:red;">
![a2a1be94e9ef247c6aab27f73a1c2b3d.png](/assets/images/agregar-hdd-via-usb-a-raspberrypi-4/a2a1be94e9ef247c6aab27f73a1c2b3d.png)
</span>

<br>

- Nos preguntará si estamos seguros, como sí lo estamos le damos a "**Aplicar**":<br>
<span align="center" style="color:red;">
![65fb624d5b051177babbad666fec77bb.png](/assets/images/agregar-hdd-via-usb-a-raspberrypi-4/65fb624d5b051177babbad666fec77bb.png)
</span>

<br>

- **Gparted** comenzará a particionar el disco y toca esperar...<br>
<span align="center" style="color:red;">
![a3b5bfd4fb7d77938e98a510c6ea26d3.png](/assets/images/agregar-hdd-via-usb-a-raspberrypi-4/a3b5bfd4fb7d77938e98a510c6ea26d3.png)
</span>

<br>

- Cuando obtengamos el siguiente mensaje, la partición habrá terminado de realizarse.<br>
Damos a "**Cerrar**" y esperamos a que termine de realizar comprobaciones del disco.<br>
<span align="center" style="color:red;">
![9d161a0b5a6491a0d9dee2cc8a01ad76.png](/assets/images/agregar-hdd-via-usb-a-raspberrypi-4/9d161a0b5a6491a0d9dee2cc8a01ad76.png)
</span>

<br>

- Una vez finalizado, observaremos que ya nos muestra los tamaños "Libre" y "Usado".<br>
<span align="center" style="color:red;">
![2f0947f54bf4e96ae1d70b67f964f79b.png](/assets/images/agregar-hdd-via-usb-a-raspberrypi-4/2f0947f54bf4e96ae1d70b67f964f79b.png)
</span>

<br>
**Se necesitará montar la partición para poder utilizarla**.

<br><br><br><br>



# Configurar montaje de Partición en FSTAB

Las particiones usadas en el sistema operativo GNU/Linux para añadirse en el directorio raíz y montar el arranque, son especificadas en el fichero "**/etc/fstab**", donde se almacena la información acerca del montaje de estas particiones.<br>

Para agregar la partición al fichero "**fstab**":

- Se debe crear un directorio (en mi caso lo crearé) en  "**/media**" con el nombre que queramos asignarle, (en mi caso) se llamará "**GitLabStorage**", por lo que el comando a ejecutar sería:<br>
```sudo mkdir /media/GitLabStorage```<br>
<span align="center" style="color:red;">
![534f23597b86ee419a355a26fe9a6d8f.png](/assets/images/agregar-hdd-via-usb-a-raspberrypi-4/534f23597b86ee419a355a26fe9a6d8f.png)
</span>

<br>

 Se necesita **el UUID de la partición** para agregar la partición al fichero FSTAB:
 
 -  **En caso de no saber cual es el nombre de la partición**, se puede mostrar las tablas de particiones con el comando "**fdisk -l**" y veremos la ruta completa de las particiones, en mi caso la partición se encuentra en "**/dev/sda1**"<br>
 ```sudo fdisk -l```<br>
<span align="center" style="color:red;">
![c3240b5acdaccef800c8780ae1292969.png](/assets/images/agregar-hdd-via-usb-a-raspberrypi-4/c3240b5acdaccef800c8780ae1292969.png)
</span>

<br>

- Para **obtener los UUID** de las particiones, ejecutamos el comando:<br>
 ```ls -l /dev/disk/by-uuid```<br>
<span align="center" style="color:red;">
![5460ebbc24b1fa039acd86aeb58f34bd.png](/assets/images/agregar-hdd-via-usb-a-raspberrypi-4/5460ebbc24b1fa039acd86aeb58f34bd.png)
</span>
 
 - Identificamos el UUID de la unidad que vamos a montar.<br>
 En mi caso es "**../../sda1**" y su respectivo UUID es "**8b38ea9d-615e-4beb-8cf5-faf435853bab**" .<br>
 Con estos datos tenemos la información necesaria para editar y agregar una nueva unidad en el archivo "**fstab**".
 <br>
 
 <br>
 
 ---
**La línea a agregar en el fichero "fstab" se compone de**:
 
```<file system> <dir> <type> <options> <dump> <pass>```<br>
 
**file system**: La ID correspondiente a la partición/disco.<br>
**dir**: Ruta donde queremos que se monte la partición.<br>
**type**: Se especifica el tipo de partición en este caso en "ext4" pero puede establecerse en “auto”.<br>
**options**: Se establece con la opción “defaults”.<br>
**dump** y **pass**: Se establecen en 0 0.<br>

---
<br>

Por lo tanto la línea que **en mi caso** tengo que agregar en "**fstab**" es:<br>

```UUID=8b38ea9d-615e-4beb-8cf5-faf435853bab /media/GitLabStorage  ext4	defaults	0	0```

<br>
- Procedemos a agregar la línea en el archivo "**fstab**".<br>
Abrimos el fichero, Pegamos la línea, Guardamos y Cerramos:<br>
```sudo nano /etc/fstab```<br>
<span align="center" style="color:red;">
![1010025be89057873784d37ef8eaa7b8.png](/assets/images/agregar-hdd-via-usb-a-raspberrypi-4/1010025be89057873784d37ef8eaa7b8.png)
</span>
<br><br>

- Probamos a montar todas las unidades contenidas en el "fstab":<br>
```sudo mount -a```<br>
<span align="center" style="color:red;">
![5def623b46ca6b1fc168e2bad89e0b1d.png](/assets/images/agregar-hdd-via-usb-a-raspberrypi-4/5def623b46ca6b1fc168e2bad89e0b1d.png)
</span>

<br>

- Si todo se realiza correctamente, encontraremos nuestra nueva unidad montada en el explorador de archivos.<br>
<span align="center" style="color:red;">
![c76ef2f625de7c4d108b803b48a4c5ff.png](/assets/images/agregar-hdd-via-usb-a-raspberrypi-4/c76ef2f625de7c4d108b803b48a4c5ff.png)
</span>

<br>

**Nuestro nuevo disco está listo para su uso y se montará automáticamente al inicio del sistema.**
<br><br><br><br>



