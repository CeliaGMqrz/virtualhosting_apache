# Introducción APACHE 2.4

## Conceptos básicos

Es un servidor web HTTP de código abierto, multiplataforma e implementa el protocolo HTTP/1.1. Se desarrolla dentro del proyecto HTTP Server (httpd) de la Apache Software Fundation. Apache es el servidor HTTP más usado y es muy estable.

Curiosidades: ¿Porqué se llama Apache?

Al principio el servidor no funcionaba bien y le añadían 'parches'. Le llamaban "a patchy server" y por eso, al final se le quedó el nombre de Apache.

## Ventajas de usar Apache2

* Es modular, se pueden instalar módulos como PHP.
* Código abierto.
* Multi-plataforma.
* Extensible.
* Popular

## Instalación de Apache2 en Debian/Ubuntu

* Para instalarlo usamos el siguiente comando:

```sh
apt-get install apache2

```
* Gestionar el servicio con apache.

```sh
apache2ctl [-k start|restart|graceful|graceful-stop|stop]
```

* Gestionar el servicio con systemd.

```sh
systemctl [start|stop|restart|reload|status] apache2.service
```

## Ficheros de configuración 

El fichero de configuración de apache2 en Debian era muy extenso y lo dividieron. Tiene una configuración descentralizada. Es **/etc/apache2/apache2.conf**.

En ese fichero se determina el directorio de nuestro sitio web. Todas las opciones que configuremos en **Directory** se aplicarán a **/var/www/**

![captura3.png](https://github.com/CeliaGMqrz/virtualhosting_apache/blob/main/capturas/captura3.png)

