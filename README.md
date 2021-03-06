# VirtualHosting con Apache

![banner_apache.png](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/banner_apache.png)

## Conceptos previos

### ¿Qué es un sitio web?

Un sitio web es un conjunto de páginas web, propiamente dicho. Las páginas están relacionadas entre sí. Y a ese sitio web se accede en una dirección ip y a través de un puerto(80). Normalmente si cambiamos la ip o el puerto serviría otro sitio web.

### ¿Qué es Hosting Virtual?

El término **Hosting Virtual** se refiere a hacer funcionar más de un sitio web (tales como www.company1.com y www.company2.com) en una sola máquina. Los sitios web virtuales pueden estar “basados en direcciones IP”, lo que significa que cada sitio web tiene una dirección IP diferente, o “basados en nombres diferentes”, lo que significa que con una sola dirección IP están funcionando sitios web con diferentes nombres (de dominio). Apache fue uno de los primeros servidores web en soportar hosting virtual basado en direcciones IP.

En definitiva:

Usando virtualhosting, el servidor desde una misma ip y desde el mismo puerto puede servir **varios sitios web**. Y cada uno podemos acceder por separado. Esto lo hace de manera que las peticiones al servidor se hacen con un **nombre** configurado en la **cabecera host del servidor**. Dependiendo del nombre con el que se acceda al servidor servirá un sitio o otro. 

En Apache hay configurado un virtualhost por defecto. Se llama **default**. El server name no está indicado, es decir que podemos entrar con cualquier nombre o con la misma dirección ip. El document root donde están los ficheros es **/var/www/html**.


TAREAS:

* EJERCICIO 3 -> [Configuración de virtualhosting con apache](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/configuracion.md)
* EJERCICIO 4 -> [Mapear URL a ubicaciones de un sistema de ficheros](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/mapear_url.md)
* EJERCICIO 5 -> [Control de acceso, autentificación y autorización](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/ej5_control_acceso.md)
* EJERCICIO 6 -> [Configuración de apache mediante archivo .htaccess](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/ej6_htaccess.md)


ÍNDICE:

1. [Introducción HTTP](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/introduccion_http.md)
2. [Introducción Apache](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/introduccion_apache.md)
3. [Configuración de VirtualHosting con Apache](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/configuracion.md)
4. [Otras configuraciones VirtualHost](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/otras_configuraciones.md)
5. [Mapeo I. Directivas y Opciones](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/mapeo1.md)
6. [Mapeo II. Mapear URL a ubicaciones de un sistema de ficheros](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/mapear_url.md)
7. [Control de acceso, autentificación y autorización](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/ej5_control_acceso.md)
8. [Configuración de apache mediante archivo .htaccess](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/ej5_control_acceso.md)
