# Introducción Protocolo HTTP

**HTTP** es un protocolo de comunicaciones estándar que comunica servidores, proxies y clientes. Permite la transferencia de documentos web, sin importar cual es el cliente o cual es el servidor. Es un protocolo basado en el esquema **peticion-respuesta**.

### Funcionamiento:

El usuario escribe una URL, indicando el protocolo, servidor y recurso que quiere obtener, el servidor procesa la informacion y devuelve un mensaje de respuesta, normalmente con el HTML de la página que se va a visualizar.

captura1.png

**En la cabecera host se indica el nombre del servidor que hemos puesto en la URL.**

## Métodos de envío de datos (Petición)

* **GET**: Solicita un documento al servidor
* **HEAD**: Similar a Get, pero sólo pide cabeceras HTTP.
* **POST**: Manda datos al servidor para su procesado.
* **PUT**: Almacena el documento enviado en el cuerpo del mensaje.
* **DELETE**: Elimina un documento referenciado en la URL.


NOTA:
Cuando quiero cambiar algo en el estado del servidor utilizamos POST. En el cuerpo de la petición van los datos que quiero enviar al servidor. 

## Código de estados (Respuesta)

captura2.png

## Cabeceras

En la URL hay Tiene tres partes, protocolo, nombre del servidor(se guarda en el host), el camino del recurso que quiere obtener y parámetros.

## Otras características

### Cookies y Sesiones

Como la función del servidor es peticion/respuesta se necesita un sitio donde almacenar información como el nombre de usuario y la contraseña de una página web, o los artículos insertados en el carrito de la compra. Esa información está guardada en el cliente (cookies), o en el servidor (sesión).

### Autentificación y Conexiones persistentes

A veces, debido a cuestiones de personalización o políticas de restricción, las aplicaciones web deben conocer e identificar la identidad del usuario, mediante usuario y contraseña.

Las conexiones persistentes permiten que varias peticiones y respuestas sean transferidas usando la misma conexión TCP.