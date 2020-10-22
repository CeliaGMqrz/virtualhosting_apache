# Mapear URL a ubicaciones de un sistema de ficheros

## Conocimientos previos. Repaso.

### 1. **Alias**: Un alias me permite servir ficheros que no se encuentran en el DocumentRoot.

La directiva **Alias** nos permite que el servidor sirva ficheros desde cualquier ubicación del sistema de archivo aunque esté fuera del directorio indicado en el **DocumentRoot**.

<pre>Alias <url> <sistema de archivos></pre>

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

* All.
* FollowSymLinks: Enlace simbolico apunta a un directorio.
* Indexes: Si no encuentra el index.html muestra un indice de ficheros que se encuentran ahí.
* MultiViews: Negociacion de contenido, dependiendo de algunas configuraciones del cliente el servidor sirve un contenido o sirve otro.
* SymLinksIfOwnerMatch: El enlace te muestra el que apunta si el propietario del enlace es el propietario del fichero
* ExecCGI. (Ya no se utiliza)

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

### 1. Cuando se entre a la dirección www.mapeo.com se redireccionará automáticamente a www.mapeo.com/principal, donde se mostrará el mensaje de bienvenida.
### 2. En el directorio principal no se permite ver la lista de los ficheros, no se permite que se siga los enlaces simbólicos y no se permite negociación de contenido. Muestra al profesor el funcionamiento. ¿Qué configuración tienes que poner?
### 3. Si accedes a la página www.mapeo.com/principal/documentos se visualizarán los documentos que hay en /home/usuario/doc. Por lo tanto se permitirá el listado de fichero y el seguimiento de enlaces simbólicos siempre que el propietario del enlace y del fichero al que apunta sean el mismo usuario. Explica bien y pon una prueba de funcionamiento donde se vea bien el seguimiento de los enlaces simbólicos.
### 4. En todo el host virtual se debe redefinir los mensajes de error de objeto no encontrado y no permitido. Para el ello se crearan dos ficheros html dentro del directorio error. Entrega las modificaciones necesarias en la configuración y una comprobación del buen funcionamiento.
