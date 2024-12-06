# Práctica de HTTP

## Configuración de un servidor web
En esta práctica vamos a configurar un servidor web [Apache](http://httpd.apache.org/) y vamos a probar su comportamiento mandando peticiones HTTP y viendo las respuestas.

En las máquinas del laboratorio está instalada la versión 2.4.57 de Apache en `/usr/sbin/apache2`. Por defecto Apache lee el fichero de configuración de `/etc/apache2/apache2.conf`. Nosotros le indicaremos que lo lea de nuestra cuenta con la opción `-f` de línea de comando:
```
/usr/sbin/apache2 -f PATH_COMPLETO/apache2.conf
```
Como punto de partida tomaremos el fichero [apache2.conf](apache2.conf) de ejemplo. En este fichero todas las líneas que empiezan por `#` son comentarios. Como veréis, está muy comentado y es casi autoexplicativo, ¡aunque también muy largo!  
Veremos el significado de las variables (en Apache se denominan "directivas") más importantes, aquellas que necesitamos modificar para particularizar nuestro servidor web. Para más información ver la documentación: [directives](http://httpd.apache.org/docs/2.4/en/mod/directives.html) o [quickreference](http://httpd.apache.org/docs/2.4/en/mod/quickreference.html).

Primero debemos modificar ciertas directivas para que apunten a ficheros que estén en nuestra cuenta o para que den datos sobre quién somos:
- `DefaultRuntimeDir` y `ServerRoot`: indica a partir de qué directorio se encuentran los ficheros necesarios para el funcionamiento del servidor como por ejemplo, `apache2.conf`. Configurarlo para que sea un directorio de nuestra cuenta.
- `PidFile` y `ErrorLog`: deben apuntar a ficheros dentro de directorios locales de nuestra cuenta. Si indicamos un _path_ absoluto, no se tiene en cuenta la directiva `ServerRoot` para obtener el nombre completo de los ficheros.
- `ServerAdmin`: nuestra dirección de correo. Esta dirección aparecerá en las páginas web generadas por el servidor cuando hay un error.
- `ServerName`: `<nombre de dominio>:<puerto>` de nuestro servidor web.

Ahora debemos modificar el puerto en el que escucha. Por defecto es el `80`, pero nosotros lo pondremos en un puerto no reservado, por ejemplo el `18xxx`, donde `xxx` son los tres últimos dígitos del puesto en el que estamos haciendo la práctica. Esto se hace modificando la directiva `Listen`.

Para especificar dónde están las páginas web que sirve haremos lo siguiente:

1. Modificaremos la directiva `DocumentRoot` que indica al servidor cuál es el directorio donde se encuentran los documentos que debe mostrar.
2. Crearemos en este directorio una página HTML muy simple, `www/aptel.html`, por ejemplo:
    ```html
    <html>
      <body>
        noot noot!
      </body>
    </html>
    ```

Cuando introduzcamos en el navegador una URL del estilo `http://nombre_servidor:puerto/directorio_local/`, en la que no especificamos la página HTML a mostrar, el servidor puede actuar de dos maneras diferentes. Por un lado, puede mostrarnos un listado del directorio (no recomendable) o, por otro, mostrarnos el contenido de una página HTML por defecto. Para conseguir esta última opción, debemos definir la variable `DirectoryIndex`, de tal forma que cuando no se indique una página en concreto, se muestre una por defecto (normalmente `index.html`) que debe residir en el directorio al que se accede. Observe que en el fichero `apache2.conf` que ha copiado está definida esta directiva y de momento no es necesario que la modifique. En caso de que no exista una página por defecto, se puede utilizar la directiva `Options -Indexes` para evitar que se presente el listado del directorio.

> [!NOTE]
> En el fichero `apache2.conf` hay directivas `LoadModule` o `Include`. Éstas permiten cargar módulos que dan funcionalidades adicionales a Apache, por ejemplo soporte de PHP o de sockets seguros (SSL). En esta práctica de momento no vamos a cambiar estas directivas.


## Ejercicios

### Ejercicio 1
> Lance el servidor web y acceda a él con un navegador. ¿Qué ve en la URL `http://<su_maquina>:<su_puerto>/aptel.html`? ¿y en `http://<su_maquina>:<su_puerto>/`?
```
$ curl http://localhost:18021/aptel.html
<html>
  <body>
    noot noot!
  </body>
</html>
```
```
$ curl http://localhost:18021/
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>404 Not Found</title>
</head><body>
<h1>Not Found</h1>
<p>The requested URL was not found on this server.</p>
</body></html>
```

> ¿Qué pasa si redefine la directiva `DirectoryIndex` para que apunte a `aptel.html`?
> ```xml
> <IfModule dir_module>
>     DirectoryIndex aptel.html
> </IfModule>
> ```

>[!TIP]
> Recuerde que para reiniciar el servidor debe matar el proceso con `kill`, cuyo ID se encuentra en el archivo definido en `PidFile`.
> Por ejemplo:
> ```bash
> kill $(cat ~/web/var/apache.pid)
> ```

Ahora redirige automáticamente a la página que hemos definido.
```
$ curl http://localhost:18021/aptel.html
<html>
  <body>
    noot noot!
  </body>
</html>
```


### Ejercicio 2
> Pruebe a hacer estas mismas consultas emulándolas con un `telnet` al puerto correspondiente. Esto es tan sencillo como invocarlo con el comando `telnet <máquina> <puerto>` e ir escribiendo las peticiones una vez esté establecida la conexión. Haga la petición como HTTP/0.9 y HTTP/1.0. ¿Qué cabeceras de respuesta obtiene?

En HTTP/0.9 ninguna, y en HTTP/1.0 varias.
```
$ telnet localhost 18021
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
GET /
<html>
  <body>
    noot noot!
  </body>
</html>
Connection closed by foreign host.
```
```
$ telnet localhost 18021
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
GET / HTTP/1.0

HTTP/1.1 200 OK
Date: Fri, 06 Dec 2024 12:38:08 GMT
Server: Apache/2.4.57 (Debian) OpenSSL/3.0.9
Last-Modified: Fri, 06 Dec 2024 12:03:34 GMT
ETag: "31-62898cde41ec5"
Accept-Ranges: bytes
Content-Length: 49
Connection: close
Content-Type: text/html

<html>
  <body>
    noot noot!
  </body>
</html>
Connection closed by foreign host.
```


### Ejercicio 3
> Haga ahora la petición como HTTP/1.1. ¿Qué cabecera debe incluir en la petición? ¿cierra el servidor la conexión inmediatamente? ¿qué debe incluir en la petición para que lo haga? Pruébelo.
```
$ telnet localhost 18021
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
GET / HTTP/1.1
Host: localhost:18021

HTTP/1.1 200 OK
Date: Fri, 06 Dec 2024 12:41:32 GMT
Server: Apache/2.4.57 (Debian) OpenSSL/3.0.9
Last-Modified: Fri, 06 Dec 2024 12:03:34 GMT
ETag: "31-62898cde41ec5"
Accept-Ranges: bytes
Content-Length: 49
Content-Type: text/html

<html>
  <body>
    noot noot!
  </body>
</html>
```

Es necesario incluír la cabecera `Host`. El servidor no cierra la conexión de inmediato.

Para que la cierre de inmediato, se debe añadir la cabecera `Connection: close`:
```
$ telnet localhost 18021
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
GET / HTTP/1.1
Host: localhost:18021
Connection: close

HTTP/1.1 200 OK
Date: Fri, 06 Dec 2024 12:43:46 GMT
Server: Apache/2.4.57 (Debian) OpenSSL/3.0.9
Last-Modified: Fri, 06 Dec 2024 12:03:34 GMT
ETag: "31-62898cde41ec5"
Accept-Ranges: bytes
Content-Length: 49
Connection: close
Content-Type: text/html

<html>
  <body>
    noot noot!
  </body>
</html>
Connection closed by foreign host.
```


### Ejercicio 4
> Aún si no manda nada en la petición HTTP/1.1 para que cierre la conexión de forma inmediata, el servidor lo hace tras un tiempo de inactividad. Mida cuánto es este tiempo. Compruebe que coincide con el que viene especificado en la directiva `KeepAliveTimeout`.


### Ejercicio 5
> Mire el fichero `apache2.conf` y diga para qué sirven las directivas `KeepAlive` y `MaxKeepAliveRequests`.

`KeepAlive` define si el servidor va a esperar antes de cerrar la conexión, y `MaxKeepAliveRequests` controla el máximo número de requests que permitir con una conexión persistente.


### Ejercicio 6
> Mire cuántos procesos se están ejecutando. Para ello ejecute el comando `ps x`. Este número se puede controlar con la directiva `StartServers`. El servidor arranca inicialmente éste número de procesos hijo para atender tráfico, aunque arranca y para dinámicamente nuevos procesos hijo según lo necesita (el número máximo se controla con la directiva `MaxClients`, ver también `MinSpareServers` y `MaxSpareServers`).

```
$ ps x
    PID TTY      STAT   TIME COMMAND
  82354 ?        S      0:00 sshd: 0429021@pts/1
  82356 pts/1    Ss     0:00 -bash
 100558 ?        Ss     0:00 /usr/sbin/apache2 -f /usr/lab/alum/0429021/web/apache2.conf
 100559 ?        Sl     0:00 /usr/sbin/apache2 -f /usr/lab/alum/0429021/web/apache2.conf
 100560 ?        Sl     0:00 /usr/sbin/apache2 -f /usr/lab/alum/0429021/web/apache2.conf
 100562 ?        Sl     0:00 /usr/sbin/apache2 -f /usr/lab/alum/0429021/web/apache2.conf
 100564 ?        Sl     0:00 /usr/sbin/apache2 -f /usr/lab/alum/0429021/web/apache2.conf
 100566 ?        Sl     0:00 /usr/sbin/apache2 -f /usr/lab/alum/0429021/web/apache2.conf
 103630 ?        Sl     0:00 /usr/sbin/apache2 -f /usr/lab/alum/0429021/web/apache2.conf
 109428 pts/1    R+     0:00 ps x
```


### Ejercicio 7
> Modifique el fichero `apache2.conf` para que se arranquen por defecto 8 procesos hijo.

```xml
<IfModule mpm_prefork_module>
    StartServers          8
    MinSpareServers       5
    MaxSpareServers      10
    MaxClients          150
    MaxRequestsPerChild  10
</IfModule>
```


### Ejercicio 8
> Rearranque el servidor y compruebe que los cambios que ha hecho han surtido efecto.
```
$ ps x
    PID TTY      STAT   TIME COMMAND
  82354 ?        S      0:00 sshd: 0429021@pts/1
  82356 pts/1    Ss     0:00 -bash
 112428 ?        Ss     0:00 /usr/sbin/apache2 -f /usr/lab/alum/0429021/web/apache2.conf
 112429 ?        Sl     0:00 /usr/sbin/apache2 -f /usr/lab/alum/0429021/web/apache2.conf
 112430 ?        Sl     0:00 /usr/sbin/apache2 -f /usr/lab/alum/0429021/web/apache2.conf
 112432 ?        Sl     0:00 /usr/sbin/apache2 -f /usr/lab/alum/0429021/web/apache2.conf
 112434 ?        Sl     0:00 /usr/sbin/apache2 -f /usr/lab/alum/0429021/web/apache2.conf
 112436 ?        Sl     0:00 /usr/sbin/apache2 -f /usr/lab/alum/0429021/web/apache2.conf
 112438 ?        Sl     0:00 /usr/sbin/apache2 -f /usr/lab/alum/0429021/web/apache2.conf
 112440 ?        Sl     0:00 /usr/sbin/apache2 -f /usr/lab/alum/0429021/web/apache2.conf
 112442 ?        Sl     0:00 /usr/sbin/apache2 -f /usr/lab/alum/0429021/web/apache2.conf
 112723 pts/1    R+     0:00 ps x
```



### Ejercicio 9
> El registro de errores está en el fichero indicado por la directiva `ErrorLog`. Con la directiva `LogLevel` se puede variar el nivel de detalle del registro. Vea el fichero de registro de errores, aumente el nivel de detalle a `debug`, arranque de nuevo el servidor y compruebe cómo para el mismo tipo de peticiones se almacena un mayor número de registros.



### Ejercicio 10
> El servidor Apache genera unos registros (`.log`) interesantes sobre su funcionamiento. Por un lado, el registro de errores y por otro el de accesos.
> La directiva `CustomLog` nos permite indicar el fichero donde se va a guardar registro de los accesos a nuestro web. Configure un valor para esta directiva y examine el fichero de registro de accesos.

```
CustomLog var/access.log
```

```
$ curl http://localhost:18021/
<html>
  <body>
    noot noot!
  </body>
</html>

$ cat var/access.log
127.0.0.1 - - [06/Dec/2024:13:57:23 +0100] "GET / HTTP/1.1" 200 49
```


### Ejercicio 11
> Copie el fichero `aptel.html` a `prueba.aptel`. Haga la consulta `http://<su_maquina>:<su_puerto>/prueba.aptel` tanto con un navegador web como con un telnet. ¿Cómo ve el fichero en el navegador? ¿cuál es el tipo MIME con el que se nos envía el fichero? ¿por qué?

> [!TIP]
> Como habrá visto al hacer las consultas de las preguntas [2](#ejercicio-2) y [3](#ejercicio-3), el servidor nos dice el tipo MIME (`Content-Type`) del objeto que nos envía. Para saber el tipo MIME de un objeto, consulta la extensión del mismo en el fichero indicado en la directiva `TypesConfig`.

```
$ telnet localhost 18021
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
GET /prueba.aptel HTTP/1.0

HTTP/1.1 200 OK
Date: Fri, 06 Dec 2024 13:02:41 GMT
Server: Apache/2.4.57 (Debian) OpenSSL/3.0.9
Last-Modified: Fri, 06 Dec 2024 13:00:28 GMT
ETag: "31-62899995e58ee"
Accept-Ranges: bytes
Content-Length: 49
Connection: close

<html>
  <body>
    noot noot!
  </body>
</html>
Connection closed by foreign host.
```

Al no saber el tipo MIME de objetos, no especifica el tipo en la respuesta.





### Ejercicio 12
> Cambie la configuración para que cuando la extensión es `.aptel` devuelva el tipo MIME `application/x-tipo-aptel`. (Esto podría hacerse modificando directamente el fichero `mime.types` pero no es la metodología recomendada. Documéntese sobre la directiva `AddType` para poder añadir el nuevo tipo). Compruebe que funciona correctamente. ¿Qué hace ahora el navegador web?

```
AddType application/x-tipo-aptel .aptel
```
```
$ telnet localhost 18021
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
GET /prueba.aptel HTTP/1.0

HTTP/1.1 200 OK
Date: Fri, 06 Dec 2024 13:10:48 GMT
Server: Apache/2.4.57 (Debian) OpenSSL/3.0.9
Last-Modified: Fri, 06 Dec 2024 13:00:28 GMT
ETag: "31-62899995e58ee"
Accept-Ranges: bytes
Content-Length: 49
Connection: close
Content-Type: application/x-tipo-aptel

<html>
  <body>
    noot noot!
  </body>
</html>
Connection closed by foreign host.
```


### Ejercicio 13
> Cree el directorio `<DocumentRoot>/interno` y copie en él el fichero `aptel.html`. ¿Qué pasa ahora cuando intenta acceder con un navegador a la URL `http://<su_maquina>:<su_puerto>/interno/aptel.html`?

Devuelve la página correctamente.


### Ejercicio 14
> Tiene que generar el fichero de claves (`<DocumentRoot>/.htpasswd`). Se hace con la utilidad `htpasswd`. Cree con esta utilidad un fichero con un usuario `aptel` y password `aplicaciones`. Pruebe a entrar con este usuario y password usando un navegador web.

```
htpasswd -c .htpasswd aptel
```



> [!TIP]
> Puede cambiar la configuración para un directorio concreto mediante la directiva `<Directory>`. Por ejemplo, añada el siguiente código al final de su fichero `apache2.conf` (sustituyendo `<DocumentRoot>` por el path completo del directorio raíz de su web):
> ```
> <Directory <DocumentRoot>/interno>
>     AuthType Basic
>     AuthName "Aplicaciones Telematicas"
>     AuthUserFile .htpasswd
>     Require user aptel
> </Directory>
> ```

```
$ curl http://localhost:18021/interno/aptel.html
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>401 Unauthorized</title>
</head><body>
<h1>Unauthorized</h1>
<p>This server could not verify that you
are authorized to access the document
requested.  Either you supplied the wrong
credentials (e.g., bad password), or your
browser doesn't understand how to supply
the credentials required.</p>
</body></html>
```

```
$ curl -u aptel:aplicaciones http://localhost:18021/interno/aptel.html
<html>
  <body>
    noot noot!
  </body>
</html>
```


### Ejercicio 15
> Inténtelo ahora usando `telnet`. ¿Cuál es el nombre del _realm_? ¿De dónde lo obtiene el servidor? Mande una segunda petición para conseguir acceder a la página.

> [!TIP]
> Puede usar un [conversor a base64](https://www.base64encode.org/) para codificar las credenciales (`aptel:aplicaciones`)

El _realm_ es el `AuthName`, `Aplicaciones Telematicas`.

```
$ telnet localhost 18021
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
GET /interno/aptel.html HTTP/1.0
Authorization: Basic YXB0ZWw6YXBsaWNhY2lvbmVz

HTTP/1.1 200 OK
Date: Fri, 06 Dec 2024 17:27:13 GMT
Server: Apache/2.4.57 (Debian) OpenSSL/3.0.9
Last-Modified: Fri, 06 Dec 2024 17:21:34 GMT
ETag: "31-6289d3f1ff6f5"
Accept-Ranges: bytes
Content-Length: 49
Connection: close
Content-Type: text/html

<html>
  <body>
    noot noot!
  </body>
</html>
Connection closed by foreign host.
```


### Ejercicio 16
> Cambie la configuración para que la autenticación sea de tipo `Digest`. Observe con un telnet cómo es la respuesta. ¿En qué se diferencia? ¿qué enviaría ahora el cliente? ¿qué ventajas tiene frente a la autenticación `Basic`? Pruébelo accediendo con un navegador y capturando con `wireshark` el tráfico.

> [!TIP]
> No sirve el fichero creado con la utilidad `htpasswd`, necesita crear uno nuevo como se indica aquí, usando la utilidad `htdigest`.

```
htdigest -c .htdigest interno aptel
```

El servidor envía un _challenge_ que el cliente tiene que superar, y no se envían las credenciales en _plaintext_.

> [!NOTE]
> Más info en <https://jitesh117.github.io/blog/http-digest-authentication/>.


### Ejercicio 17
> La directiva `Redirect` permite redireccionar a un cliente de una URL a otra. Use esta directiva para hacer que los navegadores que visiten la URL `http://<su_maquina>:<su_puerto>/vieja` salten automáticamente a la `http://<su_maquina>:<su_puerto>/nueva`

```
Redirect permanent /vieja /nueva
```

```
$ curl localhost:18021/vieja
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>301 Moved Permanently</title>
</head><body>
<h1>Moved Permanently</h1>
<p>The document has moved <a href="http://localhost:18021/nueva">here</a>.</p>
</body></html>
```


### Ejercicio 18
> Cree un directorio `<DocumentRoot>/local` y haga que el servidor responda con la página localizada en ese directorio cuando se accede a él como `localhost` y con la de apartados anteriores cuando accedemos con el nombre DNS de su máquina.

> [!TIP]
> Las directivas `NameVirtualHost`, `<VirtualHost>` y `ServerName` permiten hacer que el servidor responda distintas páginas web, según el nombre de servidor que utilicemos para acceder a él. Éste nombre se le envía con la cabecera `Host` de HTTP/1.1.

```xml
<VirtualHost localhost>
    DocumentRoot www/local
</VirtualHost>
```

```
$ curl localhost:18021
<html>
  <body>
    local noot noot!
  </body>
</html>
```
```
$ curl monitor01:18021
<html>
  <body>
    noot noot!
  </body>
</html>
```


### Ejercicio 19
> Hoy en día, la mayor parte del tráfico HTTP no se envía directamente sobre TCP, sino sobre TLS, lo que se conoce como HTTPS. Vamos a explicar cómo desencriptar con Wireshark tráfico HTTPS. Para ellos, tendremos que decir a Wireshark qué clave utilizar para descifrarlo, de lo contrario nos aparecerá como tráfico TLS, pero no podrá mostrarnos su contenido. Para ello tenemos que hacer lo siguiente:
> - Genere una variable de entorno que indique un fichero log en el que guardar las claves: `export SSLKEYLOGFILE=$HOME/ssl-key.log` y compruebe que se ha generado correctamente con `echo $SSLKEYLOGFILE`.
> - Abra Wireshark: `Edit > Preferences > Protocols > TLS > (Pre)-Master-Secret log filename`. Seleccione el fichero de _logs_ creado anteriormente: `$HOME/ssl-key.log`, sustituyendo `$HOME` por el valor del directorio hogar de su cuenta. Reinicie Wireshark para que lea los cambios.
> - Abra en un navegador Chrome lanzado desde el terminal en el que ha configurado la variable de entorno `SSLKEYLOGFILE` la página <https://www.uc3m.es>. ¿Puede ver con Wireshark los mensajes y respuestas intercambiados?

<!-- pereza -->


### Ejercicio 20
> Una herramienta muy útil es el diagrama en cascada (_waterfall_) de chrome. Nos permite ver cómo se descargan los distintos elementos de una página web. Para verlo, tenemos que ir al menú de los tres puntos de arriba a la derecha y seleccionar `Más herramientas -> Herramientas para desarrolladores`. En la ventana que nos abre seleccionamos la pestaña `Network`. Ahora cuando abramos una URL, nos mostrará la descarga de recursos que se hace. Puede ver más información de lo que puede hacer en [Chrome DevTools Network Analysis Reference](https://developers.google.com/web/tools/chrome-devtools/network/reference). Conviene que limpiemos primero la caché del navegador. Abra <http://gaia.cs.umass.edu/wireshark-labs/HTTP-wireshark-file4.html> y copie el gráfico de cascada que genera. Pruébelo para ver la información que muestra. Genere el gráfico de cascada accediendo a una página más compleja, como <https://www.uc3m.es>.

<!-- pereza -->


### Ejercicio 21
> Vamos ahora a comparar los protocolos HTTP/1.1 y HTTP/2. Cargue la siguiente página, que sirve una imagen compuesta de 380 pequeñas teselas con ambos protocolos: <https://http2.akamai.com/demo>. Observe la diferencia de tiempos en cargar la imagen. Analice la descarga usando un diagrama de cascada, como el del apartado anterior. Observe el tráfico con wireshark, para ver los mensajes de HTTP/2.

<!-- pereza -->

