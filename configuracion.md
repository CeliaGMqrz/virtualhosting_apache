## 1. Crear los ficheros de configuración para cada sitio web

Los ficheros de configuración de los sitios webs se encuentran en el directorio **/etc/apache2/sites-available**, por defecto hay dos ficheros, uno se llama '000-default.conf' que es la configuración del sitio web por defecto. Necesitamos tener dos ficheros para realizar la configuración de los dos sitios virtuales, para ello vamos a copiar el fichero 000-default.conf:

```sh
root@mimaquina:/etc/apache2# cd sites-available/

root@mimaquina:/etc/apache2/sites-available# ls
000-default.conf  default-ssl.conf

root@mimaquina:/etc/apache2/sites-available# cp 000-default.conf iesgn.conf

root@mimaquina:/etc/apache2/sites-available# cp 000-default.conf departamentos.conf

root@mimaquina:/etc/apache2/sites-available# ls
000-default.conf  default-ssl.conf  departamentos.conf	iesgn.conf

```
De esta manera tendremos un fichero llamado **iesgn.conf** para realizar la configuración del sitio web **www.iesgn.org**, y otro llamado **departamentos.conf** para el sitio web **www.departamentosgn.org**.

## 2. Modificar los ficheros de configuración

Modificamos los ficheros iesgn.conf y departamentos.conf, para indicar el nombre que vamos ausar para acceder al host virtual (ServerName) y el directorio de trabajo (DocumentRoot).

* Editamos iesgn.conf:

```sh
        ServerName www.iesgn.org

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/iesgn
```

* Editamos departamentos.conf:

```sh
        ServerName www.departamentosgn.org

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/departamentosgn
```

## 3. Crear enlace simbólico

No es suficiente crear los ficheros de configuración de cada sitio web, es necesario crear un enlace simbólico a estos ficheros dentro del directorio **/etc/apache2/sites-enabled**, para ello:

```sh
root@mimaquina:~# a2ensite iesgn.conf
Enabling site iesgn.
To activate the new configuration, you need to run:
  systemctl reload apache2
root@mimaquina:~# a2ensite departamentos
Enabling site departamentos.
To activate the new configuration, you need to run:
  systemctl reload apache2


root@mimaquina:/etc/apache2/sites-enabled# ls
000-default.conf  departamentos.conf  iesgn.conf

```
La creación de los enlaces simbólicos se puede hacer con la instrucción **a2ensite nombre_fichero_configuracion**, para deshabilitar el sitio tenemos que borrar el enlace simbólico o usar la instrucción **a2dissite nombre_fichero_configuracion**.


## 4. Crear index.html necesario

Crea los directorios y los ficheros index.html necesarios en /var/www y reiniciamos el servicio.

* Creamos el directorio 'iesgn' con su index.html

```sh
root@mimaquina:/var/www# mkdir iesgn
root@mimaquina:/var/www# cd iesgn/
root@mimaquina:/var/www/iesgn# nano index.html

```
* Creamos el directorio 'departamentosgn' con su index.html

```sh
root@mimaquina:/var/www# mkdir departamentosgn
root@mimaquina:/var/www# cd departamentosgn/
root@mimaquina:/var/www/departamentosgn# ls
root@mimaquina:/var/www/departamentosgn# cp ../iesgn/index.html index.html
root@mimaquina:/var/www/departamentosgn# ls
index.html
root@mimaquina:/var/www/departamentosgn# nano index.html 
```

* Le damos los propietarios adecuados:

```sh
root@mimaquina:~# chown -R www-data:www-data /var/www/departamentosgn
root@mimaquina:~# chown -R www-data:www-data /var/www/iesgn

```

* Reiniciamos el servicio

```sh
# systemctl reload apache2
```

## 5. Modificar fichero hosts de nuestra máquina física

Para terminar lo único que tendremos que hacer es cambiar el fichero hosts en los clientes y poner dos nuevas líneas donde se haga la conversión entre los dos nombre de dominio y la dirección IP del servidor.

Le ponemos la dirección del sevidor, osea la ip de nuestra máquina virtual, que, en este caso está conectada por un puente a nuestra anfitriona.

```sh
  GNU nano 3.2                      /etc/hosts                                

127.0.0.1       localhost
127.0.1.1       mimaquina       mimaquina

192.168.100.143      www.iesgn.org
192.168.100.143      www.departamentosgn.org
# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters


```

* Hacemos ping a las urls desde nuestra máquina anfitriona

```sh
celia@debian:~/vagrant/virtualhosting$ ping www.departamentosgn.org
PING www.departamentosgn.org (192.168.100.143) 56(84) bytes of data.
64 bytes from www.iesgn.org (192.168.100.143): icmp_seq=1 ttl=64 time=0.406 ms
64 bytes from www.iesgn.org (192.168.100.143): icmp_seq=2 ttl=64 time=0.374 ms
^C
--- www.departamentosgn.org ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 15ms
rtt min/avg/max/mdev = 0.374/0.390/0.406/0.016 ms
celia@debian:~/vagrant/virtualhosting$ ping www.iesgn.org
PING www.iesgn.org (192.168.100.143) 56(84) bytes of data.
64 bytes from www.iesgn.org (192.168.100.143): icmp_seq=1 ttl=64 time=0.389 ms
64 bytes from www.iesgn.org (192.168.100.143): icmp_seq=2 ttl=64 time=0.396 ms
^C
--- www.iesgn.org ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 16ms
rtt min/avg/max/mdev = 0.389/0.392/0.396/0.020 ms
celia@debian:~/vagrant/virtualhosting$ 

```

* Lo vemos desde nuestro navegador en la máquina anfitriona:

**www.iesgn.org**
![captura1.jpeg]()

**www.departamentosgn.org**
![captura2.jpeg]()
