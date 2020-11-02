# Configuración de apache mediante archivo .htaccess

## Ejercicio 6

Un fichero **.htaccess** (hypertext access), también conocido como archivo de configuración distribuida, es un fichero especial, popularizado por el Servidor **HTTP Apache** que nos permite definir diferentes directivas de configuración para cada directorio (con sus respectivos subdirectorios) sin necesidad de editar el archivo de configuración principal de Apache.

Para permitir el uso de los ficheros .htaccess o restringir las directivas que se pueden aplicar usamos la directiva **AllowOverride**, que puede ir acompañada de una o varias opciones: All, AuthConfig, FileInfo, Indexes, Limit...

* All: Con esta opción se pueden usar todas las directivas permitidas.
* None: Se ignora el fichero .htaccess. Es el valor que está por defecto.
* AuthConfig: Directivas de autentificación y autorización: AuthName, AuthType, AuthUserFile, Require, …
* FileInfo: Directivas relacionadas con el mapeo de URL: redirecciones, módulo rewrite, …
* Indexes: Directiva que controlan la visualización de listado de ficheros.
* Limit: Directivas para controlar el control de acceso: Allow, Deny y Order

### Pasos previos

Date de alta en un proveedor de hosting. ¿Si necesitamos configurar el servidor web que han configurado los administradores del proveedor?, ¿qué podemos hacer? Explica la directiva AllowOverride de apache2. Utilizando archivos .htaccess realiza las siguientes configuraciones:

* Nos damos de alta en [000webhost](https://es.000webhost.com/) y le damos a la opción de subir nuestro sitio web.

* He agregado un index.html y nos aparece un archivo .htaccess

![webhost1.png](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/webhost1.png)

### 1.Habilita el listado de ficheros en la URL http://host.dominio/nas.

* Añadimos la opcion indexes a nuestro archivo .htaccess

![indexes.png](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/indexes.png)

* Creamos un directorio dentro de public llamado **nas** y colocamos un par de ficheros de prueba dentro

![nasprueba.png](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/nasprueba.png)

* Despues vamos a ver nuestra página , (en mi caso he puesto una parte de mi blog estático para que sea algo más visible)

![principalweb.png](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/principalweb.png)

* Escribimos /nas y vemos como se ha configurado perfectamente la opcion de indexes

![nasindex.png](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/nasindex.png)

![nasindex2.png](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/nasindex2.png)

### 2.Crea una redirección permanente: cuando entremos en ttp://host.dominio/google salte a www.google.es.

* Podemos hacerlo de dos formas, en la misma plataforma hay una opcion de redireccion y la agregamos ahí o bien directamente sobre el fichero .htaccess

![redireccion.png](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/redireccion.png)

![redhtaccess.png](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/redhtaccess.png)

* Comprobamos que funciona la redireccion

![comp1.png](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/comp1.png)

![comp2.png](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/comp2.png)


### 3. Pedir autentificación para entrar en la URL http://host.dominio/prohibido.

* Creamos un directorio llamado 'prohibido'

![prohibido1.png](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/prohibido1.png)

* Editamos el fichero .htaccess para configurar la autentificación

```sh
# HTID:15817498: DO NOT REMOVE OR MODIFY THIS LINE AND THE LINES BELOW
php_value display_errors 1
# DO NOT REMOVE OR MODIFY THIS LINE AND THE LINES ABOVE HTID:15817498:
Options +Indexes
# HTID:15817614: DO NOT REMOVE OR MODIFY THIS LINE AND THE LINES BELOW
Redirect 301 /google http://www.google.es
# DO NOT REMOVE OR MODIFY THIS LINE AND THE LINES ABOVE HTID:15817614:
AuthUserFile /prohibido/.pass 
AuthName "Inicio de Sesión"
AuthType Basic
Require user root
```

* Vemos como nos pide la autentificacion

![auth_prohibido.png](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/auth_prohibido.png)

* Entramos con las credenciales y vemos la página

![proib.png](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/proib.png)