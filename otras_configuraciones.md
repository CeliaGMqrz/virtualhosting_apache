# Otras configuraciones de Virtual Host

Además de la configuración por nombres existen otros tipos. Aunque la que se suele usar es la configuración basada en nombres.

## VirtualHost basado en IP 

Supongamos el siguiente escenario: 

Nuestro servidor tiene dos interfaces de red. Cada interfaz tiene una ip distinta. Queremos crear un virtualhost que no esté basado en el nombre, ya que, se va a servir por las direcciones ip del servidor. 

```sh
<VirtualHost 172.20.30.40:80>
    ServerAdmin webmaster@www1.example.com
    DocumentRoot "/www/vhosts/www1"
    ServerName www1.example.com
    ErrorLog "/www/logs/www1/error_log"
    CustomLog "/www/logs/www1/access_log" combined
</VirtualHost>
```

```sh
<VirtualHost 172.20.30.50:80>
    ServerAdmin webmaster@www2.example.com
    DocumentRoot "/www/vhosts/www2"
    ServerName www2.example.com
    ErrorLog "/www/logs/www2/error_log"
    CustomLog "/www/logs/www2/access_log" combined
</VirtualHost>
```

## Servir el mismo contenido en varias IP 

```sh
<VirtualHost 192.168.1.1 172.20.30.40>
    DocumentRoot "/www/server1"
    ServerName server.example.com
    ServerAlias server
</VirtualHost>
```

## Sirviendo distintos sitios en distintos puertos

```sh
<VirtualHost *:80>
    ServerName www.example.com
    DocumentRoot "/www/domain-80"
</VirtualHost>
```

```sh
<VirtualHost *:8080>
    ServerName www.example.com
    DocumentRoot "/www/domain-8080"
</VirtualHost>
```