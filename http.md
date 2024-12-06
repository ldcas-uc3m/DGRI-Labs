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
2. Crearemos en este directorio una página HTML muy simple, `aptel.html`, por ejemplo:
    ```
    <HTML>
      <BODY>
        Hola Mundo
      </BODY>
    </HTML>
    ```

Cuando introduzcamos en el navegador una URL del estilo `http://nombre_servidor:puerto/directorio_local/`, en la que no especificamos la página HTML a mostrar, el servidor puede actuar de dos maneras diferentes. Por un lado, puede mostrarnos un listado del directorio (no recomendable) o, por otro, mostrarnos el contenido de una página HTML por defecto. Para conseguir esta última opción, debemos definir la variable `DirectoryIndex`, de tal forma que cuando no se indique una página en concreto, se muestre una por defecto (normalmente `index.html`) que debe residir en el directorio al que se accede. Observe que en el fichero `apache2.conf` que ha copiado está definida esta directiva y de momento no es necesario que la modifique. En caso de que no exista una página por defecto, se puede utilizar la directiva `Options -Indexes` para evitar que se presente el listado del directorio.

> [!NOTE]
> En el fichero `apache2.conf` hay directivas `LoadModule` o `Include`. Éstas permiten cargar módulos que dan funcionalidades adicionales a Apache, por ejemplo soporte de PHP o de sockets seguros (SSL). En esta práctica de momento no vamos a cambiar estas directivas.


## Ejercicios

### Ejercicio 1
> Lance el servidor web y acceda a él con un navegador. ¿Qué ve en la URL `http://<su_maquina>:<su_puerto>/aptel.html`? ¿y en `http://<su_maquina>:<su_puerto>/`? ¿Qué pasa si redefine la directiva DirectoryIndex para que apunte a aptel.html?


### Ejercicio 2
> Pruebe a hacer estas mismas consultas emulándolas con un `telnet` al puerto correspondiente. Esto es tan sencillo como invocarlo con el comando `telnet <máquina> <puerto> e ir escribiendo las peticiones una vez esté establecida la conexión. Haga la petición como HTTP/0.9 y HTTP/1.0. ¿Qué cabeceras de respuesta obtiene?


### Ejercicio 3
> Haga ahora la petición como HTTP/1.1. ¿Qué cabecera debe incluir en la petición? ¿cierra el servidor la conexión inmediatamente? ¿qué debe incluir en la petición para que lo haga? Pruébelo.


### Ejercicio 4
> Aún si no manda nada en la petición HTTP/1.1 para que cierre la conexión de forma inmediata, el servidor lo hace tras un tiempo de inactividad. Mida cuánto es este tiempo. Compruebe que coincide con el que viene especificado en la directiva `KeepAliveTimeout`.


### Ejercicio 5
> Mire el fichero `apache2.conf` y diga para qué sirven las directivas `KeepAlive` y `MaxKeepAliveRequests`.


### Ejercicio 6
> Mire cuántos procesos se están ejecutando. Para ello ejecute el comando `ps x`. Este número se puede controlar con la directiva `StartServers`. El servidor arranca inicialmente éste número de procesos hijo para atender tráfico, aunque arranca y para dinámicamente nuevos procesos hijo según lo necesita (el número máximo se controla con la directiva `MaxClients`, ver también `MinSpareServers` y `MaxSpareServers`).


### Ejercicio 7
> Modifique el fichero `apache2.conf` para que se arranquen por defecto 8 procesos hijo. Añada también que el fichero `aptel.html` sea el fichero que se envía por defecto. Puede, si así lo desea, cambiar el valor de otras directivas que se han comentado en los apartados anteriores.


### Ejercicio 8
Rearranque el servidor y compruebe que los cambios que ha hecho han surtido efecto.

> [!TIP]
> Para hacer cualquier cambio de configuración en el servidor, debe primero pararlo y después rearrancarlo. Para ello use el comando `kill` y el número de proceso que se guarda en el fichero indicado en la directiva `PidFile`.


### Ejercicio 9
> El registro de errores está en el fichero indicado por la directiva `ErrorLog`. Con la directiva `LogLevel` se puede variar el nivel de detalle del registro. Vea el fichero de registro de errores, aumente el nivel de detalle a `debug`, arranque de nuevo el servidor y compruebe cómo para el mismo tipo de peticiones se almacena un mayor número de registros.

> [!TIP]
> El servidor Apache genera unos registros (`log`) interesantes sobre su funcionamiento. Por un lado, el registro de errores y por otro el de accesos.


### Ejercicio 10
> La directiva `CustomLog` nos permite indicar el fichero donde se va a guardar registro de los accesos a nuestro web. Configure un valor para esta directiva y examine el fichero de registro de accesos.


### Ejercicio 11
> Copie el fichero aptel.html en prueba.aptel. Haga la consulta `http://<su_maquina>:<su_puerto>/prueba.aptel` tanto con un navegador web como con un telnet. ¿Cómo ve el fichero en el navegador? ¿cuál es el tipo MIME con el que se nos envía el fichero? ¿por qué?

> [!TIP]
> Como habrá visto al hacer las consultas de las preguntas 2 y 3, el servidor nos dice el tipo MIME (`Content-Type`) del objeto que nos envía. Para saber el tipo MIME de un objeto, consulta la extensión del mismo en el fichero indicado en la directiva `TypesConfig`.


### Ejercicio 12
> Cambie la configuración para que cuando la extensión es `.aptel` devuelva el tipo MIME `application/x-tipo-aptel`. (Esto podría hacerse modificando directamente el fichero `mime.types` pero no es la metodología recomendada. Documéntese sobre la directiva `AddType` para poder añadir el nuevo tipo). Compruebe que funciona correctamente. ¿Qué hace ahora el navegador web?

> [!TIP]
> Puede cambiar la configuración para un directorio concreto mediante la directiva `<Directory>`. Por ejemplo, añada el siguiente código al final de su fichero `apache2.conf` (sustituyendo `DOCUMENT_ROOT` por el path completo del directorio raíz de su web):
> ```
> <Directory DOCUMENT_ROOT/interno>
>     AuthType Basic
>     AuthName "Aplicaciones Telematicas"
>     AuthUserFile DOCUMENT_ROOT/.htpasswd
>     Require user aptel
> </Directory>
> ```


### Ejercicio 13
> Cree el directorio `DOCUMENT_ROOT/interno` y copie en él el fichero `aptel.html`. ¿Qué pasa ahora cuando intenta acceder con un navegador a la URL `http://<su_maquina>:<su_puerto>/interno/aptel.html`?


### Ejercicio 14
> Tiene que generar el fichero de claves (`DOCUMENT_ROOT/.htpasswd`). Se hace con la utilidad `htpasswd`. Cree con esta utilidad un fichero con un usuario `aptel` y `password` aplicaciones. Pruebe a entrar con este usuario y password usando un navegador web.



### Ejercicio 15
> Inténtelo ahora usando `telnet`. ¿Cuál es el nombre del reino? ¿De dónde lo obtiene el servidor? Mande una segunda petición para conseguir acceder a la página.

> [!TIP]
> Puede usar un [conversor a base64](https://www.base64encode.org/).


### Ejercicio 16
> Cambie la configuración para que la autenticación sea de tipo Digest. Observe con un telnet cómo es la respuesta. ¿En qué se diferencia? ¿qué enviaría ahora el cliente? ¿qué ventajas tiene frente a la autenticación "Basic"? Pruébelo accediendo con un navegador y capturando con `wireshark` el tráfico.

> [!NOTE]
> No sirve el fichero creado con la utilidad `htpasswd`, necesita crear uno nuevo como se indica aquí, usando la utilidad `htdigest`.


### Ejercicio 17
> La directiva `Redirect` permite redireccionar a un cliente de una URL a otra. Use esta directiva para hacer que los navegadores que visiten la URL `http://<su_maquina>:<su_puerto>/vieja` salten automáticamente a la `http://<su_maquina>:<su_puerto>/nueva`


### Ejercicio 18
> Cree un directorio DOCUMENT_ROOT/local y haga que el servidor responda con la página localizada en ese directorio cuando se accede a él como localhost  y con la de apartados anteriores cuando accedemos con el nombre DNS de su máquina.

> [!NOTE]
> Las directivas `NameVirtualHost`, `<VirtualHost>` y `ServerName` permiten hacer que el servidor responda distintas páginas web, según el nombre de servidor que utilicemos para acceder a él. Éste nombre se le envía con la cabecera `Host` de HTTP/1.1.


### Ejercicio 19
> Hoy en día, la mayor parte del tráfico HTTP no se envía directamente sobre TCP, sino sobre TLS, lo que se conoce como HTTPS. Vamos a explicar cómo desencriptar con Wireshark tráfico HTTPS. Para ellos, tendremos que decir a Wireshark qué clave utilizar para descifrarlo, de lo contrario nos aparecerá como tráfico TLS pero no podrá mostrarnos su contenido. Para ello tenemos que hacer lo siguiente:
> - Genere una variable de entorno que indique un fichero log en el que guardar las claves: `export SSLKEYLOGFILE=$HOME/ssl-key.log `y compruebe que se ha generado correctamente con `echo $SSLKEYLOGFILE`.
> - Abra Wireshark: `Edit > Preferences > Protocols > TLS > (Pre)-Master-Secret log filename`. Seleccione el fichero de logs creado anteriormente: `$HOME/ssl-key.log`, sustituyendo `$HOME` por el valor del directorio hogar de su cuenta. Reinicie Wireshark para que lea los cambios.
> - Abra en un navegador chrome lanzado desde el terminal en el que ha configurado la variable de entorno `SSLKEYLOGFILE` la página <https://www.uc3m.es>. ¿Puede ver con Wireshark los mensajes y respuestas intercambiados?


### Ejercicio 20
> Una herramienta muy útil es el diagrama en cascada (_waterfall_) de chrome. Nos permite ver cómo se descargan los distintos elementos de una página web. Para verlo, tenemos que ir al menú de los tres puntos de arriba a la derecha y seleccionar `Más herramientas -> Herramientas para desarrolladores`. En la ventana que nos abre seleccionamos la pestaña `Network`. Ahora cuando abramos una URL, nos mostrará la descarga de recursos que se hace. Puede ver más información de lo que puede hacer en [Chrome DevTools Network Analysis Reference](https://developers.google.com/web/tools/chrome-devtools/network/reference). Conviene que limpiemos primero la caché del navegador. Abra <http://gaia.cs.umass.edu/wireshark-labs/HTTP-wireshark-file4.html>. y copie el gráfico de cascada que genera. Pruébelo para ver la información que muestra. Genere el gráfico de cascada accediendo a una página más compleja, como <https://www.uc3m.es>.



### Ejercicio 21
> Vamos ahora a comparar los protocolos HTTP/1.1 y HTTP/2. Cargue la siguiente página, que sirve una imagen compuesta de 380 pequeñas teselas con ambos protocolos: <https://http2.akamai.com/demo>. Observe la diferencia de tiempos en cargar la imagen. Analice la descarga usando un diagrama de cascada, como el del apartado anterior. Observe el tráfico con wireshark, para ver los mensajes de HTTP/2.


