---
layout: single
title: Instalando WireGuard + DuckDNS en RaspberryPi 3
excerpt: "En este artículo, se procederá a instalar y configurar un servicio DDNS (DNS Dinámico) para el cuál se utilizará DuckDNS y un servidor VPN de WireGuard en una Raspberry Pi 3.."
date: 2022-10-16
classes: wide
header:
  teaser: /assets/images/instalando-wireguard-+-duckdns-en-raspberrypi-3/wi_pi.png
  teaser_home_page: true
# icon: /assets/images/hackthebox.webp
categories:
  - RaspberryPi
  - VPN
  - DDNS
tags:  
  - security
  - duckdns
  - wireguard
  - 
---

<span style="color:red;">
![Image Not Found U_U](/assets/images/instalando-wireguard-+-duckdns-en-raspberrypi-3/wi_pi.png)
</span>

En este artículo, se procederá a instalar y configurar un servicio DDNS (DNS Dinámico) para el cuál se utilizará DuckDNS y un servidor VPN de WireGuard en una Raspberry Pi 3.
<br>

### ¿Qué es DDNS?

La nomenclatura “dynamic DNS” (DynDNS) hace referencia a Dynamic Domain Name System (sistema dinámico de nombres de dominio), también conocido por su abreviatura DDNS y sirve de ayuda a la hora de reenviar las direcciones IP de tu red doméstica, que cambian constantemente, a un nombre de dominio fijo.
<br>

### ¿Qué es DuckDNS?

Duck DNS es un servicio gratuito que se encarga de dirigir direcciones IP a subdominios del tipo duckdns.org. La intención es disponer de nombres fácilmente recordables y tener un servicio DDNS con conexión externa.
<br>

### ¿Qué es Wireguard?

WireGuard es una red privada virtual (VPN) centrada en la seguridad y conocida por su simplicidad y facilidad de uso. Utiliza protocolos y algoritmos criptográficos probados para proteger los datos. Desarrollado originalmente para el kernel de Linux, ahora también se puede implementar en Windows, macOS, BSD, iOS y Android.
<hr>
<br><br>

# Introducción

El motivo de configurar el servicio DDNS es la de obtener una "dirección fija" a la que apuntar y el servicio de DuckDNS se encargará de redirigir todo el tráfico que le llegue hacia nuestra red (desde la que se esté ejecutando el cliente DDNS) y de esta manera despreocuparse de que cambie la ip pública de dicha red.
<br><br>
Por otra parte, para poder contar con acceso a la red interna que deseemos y poder ver y operar con los dispositivos conectados en dicha red, se instalará un servidor VPN de  WireGuard para ``minimizar la superficie de ataque`` de la red, ya que únicamente se necesitará tener expuesto a internet el puerto por el que WireGuard necesita quedar a la escucha y para poder conectarse un cliente a través de este, necesitará el archivo de configuración de cliente de WireGuard que a su vez debe encontrarse correctamente configurado en el servidor.
<br><br>
También podemos utilizar la VPN de WireGuard para ``mantener nuestras comunicaciones seguras`` en caso de tener que conectarnos a alguna red Wifi "free" o ajena, sobre la que no sabemos nada sobre su seguridad (quien hay conectado, configuración del router, etc...) ya que estaremos conectados a través de un túnel cifrado hasta nuestra red de confianza, en la cual WireGuard se encargará de descifrar el tráfico, hacerlo llegar a internet y viceversa.
<br><br>

## Instalación y configuración del servicio DDNS de DuckDNS

<br>Accedemos a www.duckdns.org, en caso de no tener cuenta aún no importa, igualmente en la parte superior nos logueamos
con la opción que más nos guste (Twitter, Github, Redis o Google).

<span style="color:red;">
![Image Not Found U_U](/assets/images/instalando-wireguard-+-duckdns-en-raspberrypi-3/duckdns_login.png)
</span>

<br><br>En la sección "domains" de la página, procedemos a crear un subdominio con el nombre que queramos y... esté disponible.

<span style="color:red;">
![Image Not Found U_U](/assets/images/instalando-wireguard-+-duckdns-en-raspberrypi-3/duckdns_subdomain_writing.png)
</span>

<br><br>Si todo ha ido bien, podremos observar en la página un mensaje indicando que el dominio se agregó correctamente, y en la sección "domains" podremos observar el dominio creado con nuestra dirección IP pública.

<span style="color:red;">
![Image Not Found U_U](/assets/images/instalando-wireguard-+-duckdns-en-raspberrypi-3/duckdns_subdomain_created.png)
</span>

<br><br>Para proceder a instalar el servicio en nuestra RaspberryPi 3, en la página de DuckDNS, nos dirigimos al apartado "install" y en la parte inferior de la página, seleccionamos nuestro dominio.

<span style="color:red;">
![Image Not Found U_U](/assets/images/instalando-wireguard-+-duckdns-en-raspberrypi-3/duckdns_client_pre_installing.png)
</span>

<br><br>Por último, en la sección "Operating System" seleccionaremos el Sistema Operativo para el cual queremos obtener los pasos a seguir para la instalación.
Marcamos la opción en "pi" y seguimos los pasos que nos indicará configuración:

<span style="color:red;">
![https://www.duckdns.org/install.jsp ](/assets/images/instalando-wireguard-+-duckdns-en-raspberrypi-3/duckdns_client_installing.png)
</span>

<br>
Una vez finalizado los pasos indicados en la web oficial de DuckDNS, el dominio creado ya estará redirigiendo el tráfico a nuestra red y no importará que la IP de esta cambie.

<br>
<br>
Para comprobar que el DDNS funciona correctamente:
1. Realizamos un "ping" desde nuestra terminal apuntando al dominio creado, el cual deberá responder con nuestra IP Pública.
<span style="color:red;margin-left:5%;">
![Image Not Found U_U](/assets/images/instalando-wireguard-+-duckdns-en-raspberrypi-3/duckdns_ping_testing.png)
</span>
<br><br>
2. Para averiguar cuál es nuestra IP Pública, podemos dirigirnos a [https://ifconfig.me](https://ifconfig.me) donde nos mostrará nuestra IP Pública acompañada de más información sobre nuestra conexión y navegador.
<span style="color:red;margin-left:5%;">
![Image Not Found U_U](/assets/images/instalando-wireguard-+-duckdns-en-raspberrypi-3/ifconfig_me.png)
</span>
<br><br>
3. Si las IP's de los dos pasos anteriores coinciden, significa que todo funciona correctamente y podemos proceder a la instalación de WireGuard.
<br><br><br><br>

## Instalación de servidor VPN de WireGuard en RaspberryPi 3
<span style="color:red;">
![Image Not Found U_U](/assets/images/instalando-wireguard-+-duckdns-en-raspberrypi-3/wireguard_logo.svg)
</span>

<br>Abrimos un terminal de la RaspberryPi, ya sea directamente en esta por SSH, o como queramos...
<br><br>
En primer lugar, actualizamos y upgradeamos la Raspberry:
```
sudo apt update
sudo apt upgrade
```
<br>
<br> 
Una vez actualizada, procedemos a la instalación de los headers de la raspberry
```
sudo apt-get install raspberrypi-kernel-headers
```
<br>
<br>
Dado que el paquete de WireGuard se encuentra en los repositorios de la Raspberry, procederemos directamente a su instalación junto a una herramienta llamada 'qrencode', la cual utilizaremos para exportar la configuración de los clientes de la VPN a través de un QR.
```
sudo apt install wireguard wireguard-tools qrencode
```
<br>
<br>
Cuando finalice la instalación de los paquetes, podemos comprobar si WireGuard se instaló correctamente ejecutando:
```
wg
```
<br>
<br>
En el caso de no obtener salida, comprobamos si el módulo se ha cargado ejecutando:
```
sudo modprobe wireguard
```
<br>
<br>
<b>Procedemos a habilitar el PortForwarding</b> (reenvío de puertos).
<br>Para esto, abrimos para editar el fichero de configuración ``/etc/sysctl.conf``.
```
nano /etc/sysctl.conf
```
Descomentamos la línea donde dice ``"net.ipv4.ip_forward=1"``
<br>Guardamos el archivo y cerramos.
<br>
<br>
<br>
Ahora en este punto, debemos reiniciar la RaspberriPi
```
sudo reboot
```
<br>
<br>
Cuando la RaspberryPi inicie, Tenemos que comprobar que el IP Forwarding esté activo.
<br>Podemos utilizar el siguiente comando para consultarlo:
```
sudo sysctl net.ipv4.ip_forward
```
La respuesta que debe retornar es ``1``.
<br><br><span style="color:orange;">* En caso de no retornar un </span>``1``<span style="color:orange;">, el IP Forwarding se encontrará inactivo y NO SE DEBE CONTINUAR con el siguiente paso hasta activarlo.</span>
<br><br><br><br>

## Generando configuraciones para WireGuard - Servidor y Clientes
<br>
Utilizaremos una Herramienta Online que proporciona WireGuard para generar los archivos de configuración
(Recomendable marcar la ``PresharedKey``).

<br>
Para esto, nos dirigimos a [https://www.wireguardconfig.com](https://www.wireguardconfig.com) donde estableceremos los parámetros para autogenerar los archivos de configuración del Servidor y Clientes de WireGuard.

Debemos establecer los siguientes parámetros:
  - Puerto de escucha.
  - Número de Clientes a generar.
  - Endpoint (Aquí pondremos el nombre del Dominio creado en DuckDNS y el puerto de escucha).
  - Marcamos la casilla de 'Pre-Shared Keys'.
  - Por último, Generamos la configuración clicando en 'Generate Config'.
  
<br>
<span style="color:red;">
![Image Not Found U_U](/assets/images/instalando-wireguard-+-duckdns-en-raspberrypi-3/wireguard_online_config.png)
</span>
<br>
<br>
Una vez hayamos 'generado la configuración',
<br>descargamos el ZIP en la Raspberry,
<br>y extraemos los archivos contenidos en el ZIP en la carpeta ``/etc/wireguard``
<br><br><br><br>

## Iniciando Interfaz de Red y asignando permisos
<br>
En la RaspberryPi, vamos a iniciar nuestra nueva interfaz de red del servidor con este comando:
```text
sudo wg-quick up wg0
```
<br>
<br>
Nota: Si queremos parar la interfaz en algún momento podemos ejecutar:
```text
sudo wg-quick down wg0
```
<br>
<br>
Para ver su estado ejecutamos lo siguiente:
```text
sudo wg
```
<br>
<br>
Si todo está bien y queremos hacer que WireGuard se ejecute automáticamente al iniciar la Raspberry, podemos habilitarlo con este comando:
```text
sudo su
systemctl enable wg-quick@wg0
```
<br>
<br>
Procedemos a cambiar los permisos y propietarios de las carpetas y archivos de WireGuard.
Para esta operación, nos estableceremos como usuario "root":
```text
sudo su
chown -R root:root /etc/wireguard/
chmod -R og-rwx /etc/wireguard/*
```
<br>
<br>
Por último y para asegurar de permitir el acceso a los clientes de la VPN, ejecutamos la siguiente regla de iptables:
```text
iptables -I INPUT 1 -i wg0 -j ACCEPT
```
<br>
<br>
Nuestro Servidor VPN de WireGuard ya estará listo para recibir clientes!!
<br>
<span style="color:orange;">
\* No olvidar abrir el puerto correspondiente a este dispositivo en el router.
</span>

<br><br><br><br>

## Conectar clientes a la VPN

Para conectar cualquier cliente (Móvil, PC, etc..) a nuestra VPN, tenemos que tener instalado un cliente de WireGuard en el dispositivo a utilizar.
<br>Para conectarnos a esta debemos importar el archivo ``".conf" del cliente correspondiente`` en el cliente del dispositivo que queramos conectar a la VPN, configurarlo a mano, o también haciendo uso de la herramienta “qrencode”, la cual nos proporcionará un QR que podemos escanear directamente desde nuestra aplicación cliente de wireguard y directamente se conectará.

<br>
Ejemplos para listar el archivo ".conf" del "cliente1":

  - Listar la configuración del "cliente1" en QR:
  ```text
  qrencode -t ANSIUTF8 < /etc/wireguard/client1.conf
  ```
  &nbsp;
  - Listar la configuración del "cliente1" por consola:
  ```text
  cat /etc/wireguard/client1.conf
  ```

<br><br><br>
## Páginas de Interés:
<br>
- Página oficial de WireGuard ---> [https://www.wireguard.com/quickstart/](https://www.wireguard.com/quickstart)
- Página oficial de DuckDNS   ---> [https://www.duckdns.org/](https://www.duckdns.org/)
