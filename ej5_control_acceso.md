# Control de acceso, autentificación y autorización

## Ejercicio 5

### Crea un escenario en Vagrant o reutiliza uno de los que tienes en ejercicios anteriores, que tenga un servidor con una red publica, y una privada y un cliente conectada a la red privada. Crea un host virtual departamentos.iesgn.org.

```sh
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.define :nodo1 do |nodo1|
    nodo1.vm.box = "debian/buster64"
    nodo1.vm.hostname = "servidor"
    nodo1.vm.network :public_network, :bridge=>"enp5s0"
    nodo1.vm.network "private_network", ip: "10.0.0.2",
      virtualbox__intnet: "local",
      auto_config: false
  end
  config.vm.define :nodo2 do |nodo2|
    nodo2.vm.box = "debian/buster64"
    nodo2.vm.hostname = "cliente"
    nodo2.vm.network "private_network",
      virtualbox__intnet: "local", ip: "10.0.0.3",
      auto_config: false
  end
end
```

* Instalamos apache2.

```sh
apt-get install apache2
```

* Creamos el host virtual *departamentos.iesgn.org*

```sh
root@servidor:/etc/apache2/sites-available# cp 000-default.conf departamentos.conf

```

* Creamos los directorios que vamos a necesitar en **/var/wwww/**

```sh
mkir departamentos
cd departamentos
nano index.html
mkdir internet intranet
cp index.html internet/
cp index.html intranet/
nano internet/index.html
nano intranet/index.html
```

* Así quedaría la estructura

```sh
root@servidor:/var/www# tree
.
├── departamentos
│   ├── index.html
│   ├── internet
│   │   └── index.html
│   └── intranet
│       └── index.html
└── html
    └── index.html

4 directories, 4 files
```

* Necesitamos cambiar el **propietario** del directorio departamentos

```sh
chown -R www-data:www-data /var/www/departamentos
```

* Comprobamos que se ha cambiado el propietario

```sh
root@servidor:~# ls -l /var/www/ | grep departamentos
drwxr-xr-x 4 www-data www-data 4096 Nov  2 13:45 departamentos

```

### 1. A la URL departamentos.iesgn.org/intranet sólo se debe tener acceso desde el cliente de la red local, y no se pueda acceder desde la anfitriona por la red pública. A la URL departamentos.iesgn.org/internet, sin embargo, sólo se debe tener acceso desde la anfitriona por la red pública, y no desde la red local.

* Configuramos el fichero departamentos.conf dentro del directorio sites-avaliable

```sh
<VirtualHost *:80>

        ServerName departamentos.iesgn.org
        ServerAlias www.departamentos.iesgn.org
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/departamentos

        <Directory /var/www/departamentos>
                Options FollowSymLinks Indexes MultiViews
                AllowOverride None
                Require all granted
        </Directory>

        <Directory /var/www/departamentos/intranet>
                Options Indexes FollowSymLinks MultiViews
                AllowOverride None
                Require all denied
                Require ip 10.0.0.3
        </Directory>

        <Directory /var/www/departamentos/internet>
                Options Indexes FollowSymLinks MultiViews
                AllowOverride None
                Order allow,deny
                Allow from all
                deny from 10.0.0.0/24
        </Directory>

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>

```

* Activamos el host virtual y reiniciamos el servidor de apache

```sh
root@servidor:/etc/apache2/sites-available# a2ensite departamentos
Enabling site departamentos.
To activate the new configuration, you need to run:
  systemctl reload apache2

root@servidor:/etc/apache2/sites-available# service apache2 restart

```

* Ahora vamos a la máquina anfitriona y en el /etc/hosts añadimos la dirección de nuestra máquina virtual con el nombre de nuestro host virtual

```sh
127.0.0.1       localhost
127.0.1.1       debian
192.168.100.162 www.departamentos.iesgn.org
# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters

```

* En la máquina cliente hacemos lo mismo pero poniendo la dirección ip de la interfaz de red  del servidor que está conectada a la red local y el nombre del host virtual.

```sh
127.0.0.1       localhost
127.0.1.1       cliente cliente
10.0.0.2        www.departamentos.iesgn.org
# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters

```
#### Comprobaciones de acceso

Comprobamos que desde el navegador de nuestra **máquina anfitriona**:

* Podemos acceder a la página principal:

![principal.png](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/principal.png)

* Podemos acceder al apartado internet:

![internet.png](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/internet.png)

* Pero no podemos acceder al apartado intranet:

![intranet.png](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/intranet.png)

Comprobamos desde la máquina **cliente** (instalamos previamente un navegador para sistemas sin entornos gráficos **lynx**):

* Podemos acceder a la pagina principal

```sh
vagrant@cliente:~$ lynx www.departamentos.iesgn.org
```

![principalcliente.png](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/principalcliente.png)

* Podemos acceder al apartado intranet

```sh
vagrant@cliente:~$ lynx www.departamentos.iesgn.org/intranet
```

![intranetcliente.png](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/intranetcliente.png)

* No podemos acceder al apartado internet

```sh
vagrant@cliente:~$ lynx www.departamentos.iesgn.org/internet
```

![internetcliente.png](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/internetcliente.png)

### 2. Autentificación básica. Limita el acceso a la URL departamentos.iesgn.org/secreto. Comprueba las cabeceras de los mensajes HTTP que se intercambian entre el servidor y el cliente. ¿Cómo se manda la contraseña entre el cliente y el servidor?. Entrega una breve explicación del ejercicio.


* Primero debemos de crear el directorio *secreto* en departamentos sin olvidar cambiar el propietario, como hemos hecho anteriormente.

```sh
root@servidor:/var/www/departamentos# ls -l
total 16
-rw-r--r-- 1 www-data www-data  182 Nov  2 13:36 index.html
drwxr-xr-x 2 www-data www-data 4096 Nov  2 13:47 internet
drwxr-xr-x 2 www-data www-data 4096 Nov  2 13:47 intranet
drwxr-xr-x 2 www-data www-data 4096 Nov  2 15:10 secreto
```

Para limitar el acceso a la pagina 'secreto' necesitamos implementar el método e autentificación básica, para ello, necesitamos crear un fichero de contraseñas que se genera mediante *htpasswd* con la ruta donde guardamos la contraseña y el nombre del que se le va dar permiso. En esa ruta debe de haber un directorio que vamos a crear ahora mismo.

* Crear el directorio 'claves'

```sh
root@servidor:/etc/apache2# mkdir claves
```

* Crear fichero de contraseñas

```sh
root@servidor:/etc/apache2# htpasswd -c /etc/apache2/claves/passwd.txt root
New password:
Re-type new password:
Adding password for user root
```

* Vemos que se ha guardado el fichero con las contraseñas

```sh
root@servidor:/etc/apache2/claves# ls
passwd.txt

root@servidor:/etc/apache2/claves# cat passwd.txt
root:$apr1$cF/a2VEa$HanMH/3gocabJWDMqj12c.

```
* Editamos el fichero de configuracion de nuestro virtual host, añadiendo el documentroot para la pagina secreta, usando el fichero de contraseñas y la autentificación básica.

```sh
nano /etc/apache2/sites-available/departamentos.conf
```
```sh
<VirtualHost *:80>

        ServerName departamentos.iesgn.org
        ServerAlias www.departamentos.iesgn.org
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/departamentos

        <Directory /var/www/departamentos>
                Options FollowSymLinks Indexes MultiViews
                AllowOverride None
                Require all granted
        </Directory>

        <Directory /var/www/departamentos/intranet>
                Options Indexes FollowSymLinks MultiViews
                AllowOverride None
                Require all denied
                Require ip 10.0.0.3
        </Directory>

        <Directory /var/www/departamentos/internet>
                Options Indexes FollowSymLinks MultiViews
                AllowOverride None
                Order allow,deny
                Allow from all
                deny from 10.0.0.0/24
        </Directory>

        <Directory /var/www/departamentos/secreto>
                AuthUserFile "/etc/apache2/claves/passwd.txt"
                AuthName "Usuario y contraseña"
                AuthType Basic
                Require valid-user
        </Directory>

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>

```

* Reiniciamos el servidor y comprobamos que funciona

![secreto.png](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/secreto.png)


![secreto2.png](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/secreto2.png)

