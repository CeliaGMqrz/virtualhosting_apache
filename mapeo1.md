
# Directivas en Virtualhosting

## Directiva: Options

* All.
* FollowSymLinks: Enlace simbolico apunta a un directorio.
* Indexes: Si no encuentra el index.html muestra un indice de ficheros que se encuentran ahí.
* MultiViews: Negociacion de contenido, dependiendo de algunas configuraciones del cliente el servidor sirve un contenido o sirve otro.
* SymLinksIfOwnerMatch: El enlace te muestra el que apunta si el propietario del enlace es el propietario del fichero
* ExecCGI. (Ya no se utiliza)

<pre>nano /etc/apache2/apache2.config</pre>
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

Si queremos quitarle alguna opción a algún sitio le agregamos estas lineas en el 'sites-avaliable' /000-default.conf: (si no le ponemos '-' o '+' sobreescriben las opciones del padre por las que hay indicada en su propio fichero de configuración.)

```sh
<Directory /var/wwww/html>
    Options -indexes +Multiviews
```
## ¿Qué es un Alias?

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

AliasMatch "/image/(.*)$" "/ftp/pub/image/$1"

## Negociación de contenidos

Un recurso puede estar disponible en diferentes representaciones. Negociación de contenidos: Apache puede escoger la mejor representación de un recurso basado en las preferencias proporcionadas por el navegador.

# ls /var/www/html/internacional
index.html.en index.html.es

Puede elegir si quiere español o inglés.

## Redirecciones

La directiva **redirect** es usada para pedir al cliente que haga otra peticón a una URL diferente. Normalmente la usamos cuando el recurso al que queremos acceder **ha cambiado de localización**.

* Redireccion Temporal (302)
* Redirección Permanente (301)

## Páginas de errores personalizadas

![diapositiva.jpeg](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/diapositiva.jpeg)

