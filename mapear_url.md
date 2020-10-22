# Mapear URL a ubicaciones de un sistema de ficheros

## Conocimientos previos. Repaso.

### 1. **Alias**: Un alias me permite servir ficheros que no se encuentran en el DocumentRoot.

La directiva **Alias** nos permite que el servidor sirva ficheros desde cualquier ubicación del sistema de archivo aunque esté fuera del directorio indicado en el **DocumentRoot**.

Alias <url> <sistema de archivos>

Ejemplo:

```sh
Alias "/image" "/ftp/pub/image"
<Directory "/ftp/pub/image">
    Require all granted
</Directoriy>
```
* Podemos usar AliasMatch para lo mismo pero con expresiones regulares.

```sh 
AliasMatch "/image/(.*)$" "/ftp/pub/image/$1"
```


### 2. Options: Determina para que sirven las siguientes opciones de funcionamiento:

* **All**.
* **FollowSymLinks**: Enlace simbolico apunta a un directorio.
* **Indexes**: Si no encuentra el index.html muestra un indice de ficheros que se encuentran ahí.
* **MultiViews**: Negociacion de contenido, dependiendo de algunas configuraciones del cliente el servidor sirve un contenido o sirve otro.
* **SymLinksIfOwnerMatch**: El enlace te muestra el que apunta si el propietario del enlace es el propietario del fichero
* **ExecCGI**. (Ya no se utiliza)

Ejemplo: 

<pre>nano /etc/apache2/apache2.config</pre>

fichero:

```sh
# Define la configuración para el directorio raiz y todos sus hijos.
<Directory />
        Options FollowSymLinks
        AllowOverride None
        # No tienen permisos para ver el contenido de /
        Require all denied
</Directory>

<Directory /usr/share>
        AllowOverride None
        Require all granted
</Directory>

<Directory /var/www/>
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
</Directory>

```
* Determina como funciona si delante de las opciones pongo el signo + o -.

Si queremos quitarle alguna opción a algún sitio le agregamos estas lineas en el 'sites-avaliable' /000-default.conf: (si no le ponemos '-' o '+' sobreescriben las opciones del padre por las que hay indicada en su propio fichero de configuración.)

```sh
<Directory /var/wwww/html>
    Options -indexes +Multiviews
```

### 3. La directiva Redirect nos permite crear redirecciones temporaless o permanentes.

La directiva **redirect** es usada para pedir al cliente que haga otra peticón a una URL diferente. Normalmente la usamos cuando el recurso al que queremos acceder **ha cambiado de localización**.

* Redireccion Temporal (302)
* Redirección Permanente (301)

### 4. Con la directiva 'ErrorDocument' se puede crear Respuesta de error personalizadas. 

Todo esto se puede llevar a cabo en el fichero **'/etc/apache2/conf-available/localized-error-pages.conf'**.

## Crear host virtual

Crea un nuevo host virtual que es accedido con el nombre **www.mapeo.com**, cuyo DocumentRoot sea **/srv/mapeo**.

* Para ello editamos el fichero 'apache2.conf'. Descomentamos las lineas de Directory para /srv. Guardamos y cerramos el fichero.

```sh
#<Directory /var/www/>
#       Options Indexes FollowSymLinks
#       AllowOverride None
#       Require all granted
#</Directory>

<Directory /srv/mapeo>
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
</Directory>

```

* Vamos a 'sites-avaliable', copiamos el fichero de conf por defecto y le ponemos el nombre de **mapeo.conf**.

```sh
root@mimaquina:/etc/apache2# cd sites-available/

root@mimaquina:/etc/apache2/sites-available# ls
000-default.conf  default-ssl.conf  departamentos.conf	iesgn.conf

root@mimaquina:/etc/apache2/sites-available# cp 000-default.conf mapeo.conf

root@mimaquina:/etc/apache2/sites-available# 
```
* Editamos el fichero **mapeo.conf** con nano:

```sh
<VirtualHost *:80>
        # The ServerName directive sets the request scheme, hostname and port that
        # the server uses to identify itself. This is used when creating
        # redirection URLs. In the context of virtual hosts, the ServerName
        # specifies what hostname must appear in the request's Host: header to
        # match this virtual host. For the default virtual host (this file) this
        # value is not decisive as it is used as a last resort host regardless.
        # However, you must set it for any further virtual host explicitly.

        ServerName www.mapeo.com
        ServerAdmin webmaster@localhost
        DocumentRoot /svr/mapeo

        # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
        # error, crit, alert, emerg.
        # It is also possible to configure the loglevel for particular
        # modules, e.g.
        #LogLevel info ssl:warn

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        # For most configuration files from conf-available/, which are
        # enabled or disabled at a global level, it is possible to
        # include a line for only one particular virtual host. For example the
        # following line enables the CGI configuration for this host only
        # after it has been globally disabled with "a2disconf".
        #Include conf-available/serve-cgi-bin.conf
</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet

```
* Creamos el directorio en srv

```sh
root@mimaquina:/srv# mkdir mapeo
root@mimaquina:/srv# ls
mapeo
```

* Creamos el enlace simbólico, le damos permisos sobre el directorio y creamos el index.html.

```sh
root@mimaquina:~# a2ensite mapeo
Enabling site mapeo.
To activate the new configuration, you need to run:
  systemctl reload apache2

root@mimaquina:~# chown -R www-data:www-data /srv/mapeo/

root@mimaquina:~# nano /srv/mapeo/index.html
root@mimaquina:~# chmod 755 /srv/mapeo/index.html

```
* Editamos el fichero hosts de nuestra maquina anfitriona

```sh

127.0.0.1       localhost
127.0.1.1       debian

192.168.100.143 www.mapeo.com
# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters

```

* Reiniciamos apache

<pre>systemctl reload apache2</pre>

* Comprobamos que tenemos ping

```sh
celia@debian:~/github/virtualhosting_apache$ ping www.mapeo.com
PING www.mapeo.com (192.168.100.143) 56(84) bytes of data.
64 bytes from www.mapeo.com (192.168.100.143): icmp_seq=1 ttl=64 time=0.577 ms
64 bytes from www.mapeo.com (192.168.100.143): icmp_seq=2 ttl=64 time=0.415 ms
^C
--- www.mapeo.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 11ms
rtt min/avg/max/mdev = 0.415/0.496/0.577/0.081 ms
```

* Comprobamos que se ve desde el navegador **www.mapeo.com**

![map1.png](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/map1.png)

### 1. Cuando se entre a la dirección www.mapeo.com se redireccionará automáticamente a www.mapeo.com/principal, donde se mostrará el mensaje de bienvenida.

* Primero deberemos crear el directorio principal y el html de bienvenida (debe de llamarse index.html para que apache te lo muestre.)

* Despues les damos los permisos

```sh
root@mimaquina:/srv/mapeo# chown -R www-data:www-data principal/
root@mimaquina:/srv/mapeo# chown -R www-data:www-data principal/index.html 
root@mimaquina:/srv/mapeo# chmod 755 principal/
root@mimaquina:/srv/mapeo# chmod 755 principal/index.html 
```
* Editamos en sites-avaliable/mapeo.conf y añadimos la redirección permanente.

```sh
        ServerName www.mapeo.com
        ServerAdmin webmaster@localhost
        DocumentRoot /srv/mapeo
        # Redirección permanente
        RedirectMatch permanent ^/$ "/principal/"
```
* Reiniciamos el servidor de apache

```sh
systemctl restart apache2
```

* Vamos al navegador y vemos como se ha redireccionado.

![map2.png](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/map2.png)


### 2. En el directorio principal no se permite ver la lista de los ficheros, no se permite que se siga los enlaces simbólicos y no se permite negociación de contenido. Muestra al profesor el funcionamiento. ¿Qué configuración tienes que poner?

* Editamos el fichero mapeo.conf

```sh
    <Directory /srv/mapeo/principal>
            Options -Indexes -FollowSymLinks -MultiViews
    </Directory>

```
* Reiniciamos el servicio

```sh
systemctl reload apache2
```

### 3. Si accedes a la página www.mapeo.com/principal/documentos se visualizarán los documentos que hay en /home/usuario/doc. Por lo tanto se permitirá el listado de fichero y el seguimiento de enlaces simbólicos siempre que el propietario del enlace y del fichero al que apunta sean el mismo usuario. Explica bien y pon una prueba de funcionamiento donde se vea bien el seguimiento de los enlaces simbólicos.




### 4. En todo el host virtual se debe redefinir los mensajes de error de objeto no encontrado y no permitido. Para el ello se crearan dos ficheros html dentro del directorio error. Entrega las modificaciones necesarias en la configuración y una comprobación del buen funcionamiento.