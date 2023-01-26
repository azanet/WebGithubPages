---
layout: single
title: Instalando GitLab en Raspberry Pi 4
excerpt: "Se procede a instalar nuestro propio servidor Git con GitLab en una Raspberry Pi 4"
date: 2023-01-26
classes: wide
header:
  teaser: /assets/images/instalar-gitlab-en-rpi4/banner_PostGitLab_Rpi4.png
  teaser_home_page: true
categories:
  - RaspberryPi
  - Git
  - GitLab
tags:
  - git
  - gitlab
  - storage
---
<span align="center" style="color:red;">
![banner_PostGitLab_Rpi4.png](/assets/images/instalar-gitlab-en-rpi4/banner_PostGitLab_Rpi4.png)
</span>
<br><br>

En esta entrada, se procede a instalar nuestro propio servidor Git con GitLab en una Raspberry Pi 4 a la que anteriormente se le agregó un HDD de 1Tb que sería destinado para GitLab.
<br>

La intención es prescindir de servicios de terceros en la mayor parte de lo posible y poder controlar el acceso a dicha información, ya que la unica forma de acceder a esta, sería estando en la red interna o a través de la <a href="https://azanet.github.io/instalando-wireguard-+-duckdns-en-raspberrypi-3/" target="_blank">VPN que fue instalada en una entrada anterior</a>.

<br><br>

# Índice
      
 [1. Introducción](https://github.com/azanet/azanet.github.io/blob/master/_posts/2023-01-26-instalar-gitlab-en-rpi4.md#1-introducci%C3%B3n)
 [2. Instalar GitLab](https://github.com/azanet/azanet.github.io/blob/master/_posts/2023-01-26-instalar-gitlab-en-rpi4.md#2-instalar-gitlab)
 [3. Configurar GitLab](https://github.com/azanet/azanet.github.io/blob/master/_posts/2023-01-26-instalar-gitlab-en-rpi4.md#3-configurar-gitlab)
 [4. Crear Usuario en GitLab](https://github.com/azanet/azanet.github.io/blob/master/_posts/2023-01-26-instalar-gitlab-en-rpi4.md#4-crear-usuario-en-gitlab)
 [5. Generar e Importar Claves SSH en GitLab](https://github.com/azanet/azanet.github.io/blob/master/_posts/2023-01-26-instalar-gitlab-en-rpi4.md#5-generar-e-importar-claves-ssh-en-gitlab)
 [6. Crear Repositorio en GitLab](https://github.com/azanet/azanet.github.io/blob/master/_posts/2023-01-26-instalar-gitlab-en-rpi4.md#6-crear-repositorio-en-gitlab)

<br><br><br>

# 1. Introducción

GitLab ofrece un portal Web muy parecido al de Github que nos ayuda a gestionar fácilmente <b>nuestro servidor Git</b>.


![gitlab_principal.png](/assets/images/instalar-gitlab-en-rpi4/gitlab_principal.png)

<br>

Para realizar esta instalación se han utilizado las instrucciones de la página oficial de Gitlab para la RaspberryPi:
- <a href="https://about.gitlab.com/install/#raspberry-pi-os" target="_blank">https://about.gitlab.com/install/#raspberry-pi-os</a><b></b>.
<br><br><br><br>

# 2. Instalar GitLab

Para comenzar la instalación de GitLab en la RaspberryPi 4:<br>
- Iniciamos sesión en Raspberry, ya sea por SSH, VNC, RDP o directamente desde el dispositivo.
![ssh_rpi.png](/assets/images/instalar-gitlab-en-rpi4/ssh_rpi.png)

<br>
 
- Instalamos las dependencias necesarias, abrimos un terminal en la Raspberry y ejecutamos:<br>
```
  sudo apt-get install curl openssh-server ca-certificates apt-transport-https perl

  curl https://packages.gitlab.com/gpg.key | sudo tee /etc/apt/trusted.gpg.d/gitlab.asc
```

<br>

- Agregamos el paquete de GitLab a los repositorios e instalamos el paquete.
```
  sudo curl -sS https://packages.gitlab.com/install/repositories/gitlab/raspberry-pi2/script.deb.sh | sudo bash
```

<br>

- Establecemos la URL por la que queramos y que sea accesible o por la IP y procedemos a instalar.
```
  sudo EXTERNAL_URL="https://192.168.1.10" apt-get install gitlab-ce
```

<br>

<b>Toca esperar pacientemente a que se instale</b>, *Mientras nos bebemos una cafetera...*

<br><br><br><br>

# 3. Configurar GitLab
Para configurar GitLab, debemos editar su correspondiente fichero de configuración que se encuentra en la ruta "<b>/etc/gitlab/gitlab.rb</b>", en este, podemos configurar la URL externa, certificados, etc...

<br>

Para editar el fichero de configuración de GitLab ejecutamos:
```
  sudo nano /etc/gitlab/gitlab.rb
```

<br>

## 3.1. Configurar Acceso HTTP

Procedemos a establecer la IP o Dominio y Puerto por el que accederemos a GitLab.
- Buscamos la linea que comienza con "<b>external_url</b>".
- En mi caso, como el servicio solo quiero que sea visible dentro de mi red, establezco como URL la "<b>IP Privada de la Raspberry</b>" y el puerto "<b>8888</b>".
- Por lo tanto el valor de  mi "<b>external_url</b>" sería "<b>http://192.168.1.10:8888</b>. "
![external_url.png](/assets/images/instalar-gitlab-en-rpi4/external_url.png)

<br><br>

## 3.2. Configurar Acceso HTTPS
En el caso que ya tengamos certificados SSL, procedemos a establecer la IP o Dominio, Puerto y los certificados SSL que se utilizarán para acceder a GitLab: 
- En el archivo "**/etc/gitlab/gitlab.rb**" localizamos la linea que comienza con "<b>external_url</b>" agregamos las lineas de 
- En mi caso, como el servicio solo quiero que sea visible dentro de mi red, establezco como URL la "<b>IP Privada de la Raspberry</b>" y el puerto "<b>8888</b>" pero esta vez empleando "**HTTPS**".
- Por lo tanto el valor de  mi "<b>external_url</b>" es "<b>https://192.168.1.10:8888</b>".
*(Sustituir los valores por los que sean adecuados para tu caso)*
![ec71476a156f69e8b65b5b22adb49dda.png](/assets/images/instalar-gitlab-en-rpi4/ec71476a156f69e8b65b5b22adb49dda.png)

<br><br>

## 3.3 Configurar Ruta de Almacenamiento de Repositorios
- Para configurar una Ruta "customizada" para almacenar los repositorios, incluir estas líneas en el archivo cambiando el valor de "**path**" por la Ruta que corresponda.
```
git_data_dirs({
 "default" => { "path" => "/var/opt/gitlab/git-data" },
 "storage1" => { "path" => "/mnt/storage1/git-data" },
 "storage2" => { "path" => "/mnt/storage2/git-data" }
})
```

- En mi caso solo he utilizado una Ruta
![6c0d90a9f114779dcf91ddf7a2e9d203.png](/assets/images/instalar-gitlab-en-rpi4/6c0d90a9f114779dcf91ddf7a2e9d203.png)
<br>


- Por último, Reconfiguramos GitLab para que se efectúen los cambios:
```
  sudo gitlab-ctl reconfigure
```
![gitlab_reconfigure.png](/assets/images/instalar-gitlab-en-rpi4/gitlab_reconfigure.png)
<br>

- Si todo ha ido bien, desde un dispositivo conectado a la red, accedemos a la URL:
  - **http://IP-Raspberry-Pi:8888**
<br>
- Y deberíamos ver la página de Login de GitLab.
![pagina_login.png](/assets/images/instalar-gitlab-en-rpi4/pagina_login.png)

<br>

Si es la primera vez que accedemos, nos logueamos con el Usuario: "<b>root</b>"  y la "<b>contraseña autogenerada</b>" que podremos encontrar en el siguiente archivo:
```
  sudo cat /etc/gitlab/initial_root_password
```
<br>

Cuando se acceda <b>por primera vez</b> al portal Web con el usuario "<b>root</b>", <b>se debe modificar la contraseña que estaba establecida por defecto</b>.

Una vez establecida la nueva contraseña, el portal nos redirigirá al panel de Login y debemos acceder empleando la nueva contraseña.
<br> <br> <br>


# 4. Crear Usuario en GitLab

- Para crear un nuevo usuario de GitLab, Iniciamos sesión con el Usuario "<b>root</b>"
- Nos dirigimos a la opción de menú "<b>Admin Area</b>"
![admin_config.png](/assets/images/instalar-gitlab-en-rpi4/admin_config.png)

<br>

- Pulsamos en el botón "<b>New User</b>".
![new_user_click.png](/assets/images/instalar-gitlab-en-rpi4/new_user_click.png)

<br>

- Rellenamos el formulario con los datos que nos solicita.
![new_user_form.png](/assets/images/instalar-gitlab-en-rpi4/new_user_form.png)
- <br>
 
- <b>Si hemos configurado el servidor de correos</b>, el nuevo usuario recibirá un email con las pautas a seguir para acceder a nuestro GitLab.
- <b>En caso de no haberlo configurado</b>, podemos acceder a los usuarios desde el portal de administración y establecer una contraseña manualmente para su primer inicio de sesión. Posteriormente el sistema le solicitará el cambio obligatorio de la contraseña cuando acceda por primera vez.
<br><br><br><br>


# 5. Generar e Importar Claves SSH en GitLab

Para poder trabajar con los repositorios desde local hacia GitLab y viceversa, es necesario configurar las claves SSH para poder conectar con el repositorio.
<br><br>

### Crear clave SSH en Linux
Para <b>generar una clave SSH</b>:

- En la Raspberry, abrimos un terminal y nos situamos en la carpeta "**.ssh**" que se encuentra en el "**Home**"" del usuario.
```
  cd ~/.ssh
```

- (En caso de no existir la carpeta, la creamos)
```
  mkdir ~/.ssh
```

- Una vez dentro de la carpeta, procedemos a generar la clave SSH necesitamos abrir un terminal y ejecutar:
```
  ssh-keygen -t rsa
```
  - Nos solicitará un nombre y una contraseña que son opcionales, aunque sería recomendable establecerlos.
![generating_ssh_key.png](/assets/images/instalar-gitlab-en-rpi4/generating_ssh_key.png)



<br>

 - Para <b>listar la Clave SSH</b> que hemos generado, desde un terminal ejecutamos:
```
  cat ~/.ssh/id_rsa.pub
```
![gitlab_rsa.png](/assets/images/instalar-gitlab-en-rpi4/gitlab_rsa.png)

- El resultado del comando ejecutado es la Clave SSH que debemos <b>Copiar</b> para Agregarla en GitLab.
<br><br>

- Por último, **Establecer los permisos** de la carpeta "**ssh**" y las "**claves SSH**" ejecutamos los comandos:
```
  chmod 700 ~/.ssh
  chmod 600 ~/.ssh/*
```
<br>
<!--- ### Crear clave SSH en Windows -->
<br><br>

### Agregar la Clave SSH a GitLab:
- Iniciamos sesión con el Usuario que queramos trabajar.
- Nos dirigimos a las Opciones de Usuario y clicamos en "**Preferences**".
![user_preferences.png](/assets/images/instalar-gitlab-en-rpi4/user_preferences.png)

<br>

-Aparecerá un menú a la izquierda en el cual debemos clicar en la opción "**SSH Keys**"
![preferences_ssh.png](/assets/images/instalar-gitlab-en-rpi4/preferences_ssh.png)

<br>

- Una vez dentro de "**SSH Keys**", observaremos el formulario a rellenar para agregar una nueva Clave de SSH.
![add_key_gitlab.png](/assets/images/instalar-gitlab-en-rpi4/add_key_gitlab.png)
<br>

  - En el campo "<b>Key</b>", tenemos que <b>pegar la Clave SSH que generamos anteriormente</b>.
  - En el campo "<b>Title</b>", establecemos el nombre de la clave, puede ser algo que nos ayude a identificar el dispositivo que la utiliza.
  - En el campo "<b>Usage type</b>", establecemos el tipo de uso para la Clave SSH, es recomendable dejarlo por defecto.
  - El campo "<b>Expiration Date</b>" es opcional, podemos darle una fecha de expiración a la Clave SSH o indicarle que nunca caduque.
  - Para finalizar, pulsamos en "<b>Add key</b>", y la clave quedará agregada.
![ssh_added.png](/assets/images/instalar-gitlab-en-rpi4/ssh_added.png)
<br>

Ya podemos comenzar a trabajar con los repositorios de GitLab en nuestro dispositivo.

<br><br><br><br>    


# 6. Crear Repositorio en GitLab

Accedemos al "<b>Página principal del Usuario</b>", y pulsamos el botón "<b>New Project</b>"
![newProject.png](/assets/images/instalar-gitlab-en-rpi4/newProject.png)
<br>

 - GitLab nos ofrece diferentes opciones para crear un nuevo repositorio:
   - Crear proyecto en blanco
   - Crear desde una plantilla
   - Importar proyecto
<br>
 - Vamos a crear un proyecto en blanco, por lo que pulsamos en "**Create blank project**"

Se mostrará un formulario donde nos solicitarán varios datos para establecer la información del repositorio y el tipo de visibilidad de este (público, privado, etc..).
![mi_test_repo.png](/assets/images/instalar-gitlab-en-rpi4/mi_test_repo.png)
<br>

 - Al pulsar en "**Create project**" la herramienta crea el proyecto y nos informa de una serie de instrucciones.


![repo_created.png](/assets/images/instalar-gitlab-en-rpi4/repo_created.png)

<br>


- Para probar que todo ha ido bien podemos abrir un terminal e intentar hacer un clone del proyecto, bien por HTTP o SSH.
- Podemos encontrar el comando para copiar en el propio repositorio clicando en el botón "**Clone**"

![clone_git.png](/assets/images/instalar-gitlab-en-rpi4/clone_git.png)
<br>

- En mi caso realizaré la clonación del repositorio por SSH,  emplearé el comando:
```
  git clone git@IP_Raspberry_Pi:Usuario/NombreDelRepositorio.git
```
![clonado_repo.png](/assets/images/instalar-gitlab-en-rpi4/clonado_repo.png)
<br>

- Si todo ha ido bien, encontraremos la carpeta del repositorio clonada en nuestro dispositivo.

![interior_repo.png](/assets/images/instalar-gitlab-en-rpi4/interior_repo.png)
<br>

- Con esto concluimos la instalación de nuestro **servidor de Git**.

<br><br><br>



