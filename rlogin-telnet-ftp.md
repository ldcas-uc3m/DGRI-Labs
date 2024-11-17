# Práctica de rlogin, Telnet, y FTP

## Ejercicios

## Ejercicio 1
Vamos a analizar el tráfico de rlogin de una máquina a otra. Elija otra máquina del laboratorio a la que va a hacer rlogin.

> [!NOTE]
> Alternativamente, puedes hacer rlogin a la misma máquina

1. Lance `tcpdump` o wireshark con las opciones necesarias para que le muestre el tráfico que le interesa y nada más (es decir, sólo tráfico rlogin entre su máquina y la otra). Además quizás le convenga capturarlo en un fichero para analizarlo posteriormente con calma.
```
tcpdump -w dumpfile.pcap -i lo tcp port 513 &
```
2. Haga rlogin a la otra máquina con `/usr/bin/rlogin <maquina>` (¡con el PATH completo!, si no el rlogin que ejecutará es en realidad un enlace a `slogin`, una versión que usa _secure sockets_, con cifrado, que viaja por otro puerto). Antes de introducir el _password_, compruebe que la secuencia de paquetes intercambiada se corresponde con la que se vio en teoría.

```
/usr/bin/rlogin 127.0.0.1
```

> [!NOTE]
> Para salir, `Ctrl`+`b`, trae a _foreground_ tcpdump con `fg`, y páralo con `Ctrl`+`c`

## Ejercicio 2
Introduzca su clave en rlogin y entre a la otra máquina. ¿Logra leer la clave con `tcpdump` (use `man ascii` para ver el valor hexadecimal que corresponde).

```
tcpdump -r dumpfile.pcap -X
```
Viene un paquete por caracter.
```
16:11:23.633898 IP localhost.login > localhost.1023: Flags [P.], seq 2:12, ack 39, win 65445, options [nop,nop,TS val 2425659107 ecr 2425659105], length 10
	0x0000:  4510 003e 5e5f 4000 4006 de48 7f00 0001  E..>^_@.@..H....
	0x0010:  7f00 0001 0201 03ff 46f8 b477 9f03 0d97  ........F..w....
	0x0020:  8018 ffa5 fe32 0000 0101 080a 9094 9ee3  .....2..........
	0x0030:  9094 9ee1 5061 7373 776f 7264 3a20       ....Password:.
16:11:23.633908 IP localhost.1023 > localhost.login: Flags [.], ack 12, win 65484, options [nop,nop,TS val 2425659107 ecr 2425659107], length 0
	0x0000:  4510 0034 a33c 4000 4006 9975 7f00 0001  E..4.<@.@..u....
	0x0010:  7f00 0001 03ff 0201 9f03 0d97 46f8 b481  ............F...
	0x0020:  8010 ffcc fe28 0000 0101 080a 9094 9ee3  .....(..........
	0x0030:  9094 9ee3                                ....
16:11:25.014559 IP localhost.1023 > localhost.login: Flags [P.], seq 39:40, ack 12, win 65484, options [nop,nop,TS val 2425660488 ecr 2425659107], length 1
	0x0000:  4510 0035 a33d 4000 4006 9973 7f00 0001  E..5.=@.@..s....
	0x0010:  7f00 0001 03ff 0201 9f03 0d97 46f8 b481  ............F...
	0x0020:  8018 ffcc fe29 0000 0101 080a 9094 a448  .....).........H
	0x0030:  9094 9ee3 43                             ....C
16:11:25.056256 IP localhost.login > localhost.1023: Flags [.], ack 40, win 65445, options [nop,nop,TS val 2425660530 ecr 2425660488], length 0
	0x0000:  4510 0034 5e60 4000 4006 de51 7f00 0001  E..4^`@.@..Q....
	0x0010:  7f00 0001 0201 03ff 46f8 b481 9f03 0d98  ........F.......
	0x0020:  8010 ffa5 fe28 0000 0101 080a 9094 a472  .....(.........r
	0x0030:  9094 a448                                ...H
16:11:25.372208 IP localhost.1023 > localhost.login: Flags [P.], seq 40:41, ack 12, win 65484, options [nop,nop,TS val 2425660846 ecr 2425660530], length 1
	0x0000:  4510 0035 a33e 4000 4006 9972 7f00 0001  E..5.>@.@..r....
	0x0010:  7f00 0001 03ff 0201 9f03 0d98 46f8 b481  ............F...
	0x0020:  8018 ffcc fe29 0000 0101 080a 9094 a5ae  .....)..........
	0x0030:  9094 a472 34                             ...r4
...
```

## Ejercicio 3
Compruebe que en su directorio `$HOME` (del servidor) no existe ningún fichero `.rhosts` y pruebe a crear un fichero con el siguiente contenido: `<hostname_cliente> <login_cliente>` donde `hostname_cliente` es el nombre de la máquina en la que se encuentra conectado, y `login_cliente` el nombre de usuario.
1. ¿Que ocurre si intenta de nuevo realizar una conexión rlogin? Compruebe que el comportamiento es el mismo si en lugar de utilizar el hostname de la máquina utiliza su dirección IP.
No pide credenciales.
2. ¿Que tendría que escribir en ese fichero si quisiera darse acceso desde cualquier máquina?
Poner `+ <login_cliente>`

## Ejercicio 4
Haga `rlogin` a otra máquina. Una vez dentro de la otra máquina, haga un `cat` de un fichero largo y haga `^S` para parar la salida. Capture esto y observe qué ha pasado cuando ha hecho el `^S`. Haga ahora `^C` y observe lo que pasa.

Para sacar basura por pantalla: `base64 /dev/urandom`
```
16:59:50.280312 IP localhost.1023 > localhost.login: Flags [.], ack 6504837, win 0, options [nop,nop,TS val 2428565754 ecr 2428552714], length 0
16:59:53.242685 IP localhost.1023 > localhost.login: Flags [.], ack 6504837, win 65535, options [nop,nop,TS val 2428568716 ecr 2428552714], length 0
16:59:53.242715 IP localhost.login > localhost.1023: Flags [.U], seq 6504837:6537604, ack 112, win 65401, urg 65535, options [nop,nop,TS val 2428568716 ecr 2428568716], length 32767
16:59:53.242729 IP localhost.login > localhost.1023: Flags [.U], seq 6537604:6570371, ack 112, win 65401, urg 65535, options [nop,nop,TS val 2428568716 ecr 2428568716], length 32767
16:59:53.242759 IP localhost.1023 > localhost.login: Flags [.], ack 6537604, win 65535, options [nop,nop,TS val 2428568716 ecr 2428568716], length 0
16:59:53.242776 IP localhost.login > localhost.1023: Flags [.U], seq 6570371:6603138, ack 112, win 65401, urg 65535, options [nop,nop,TS val 2428568716 ecr 2428568716], length 32767
16:59:53.256284 IP localhost.login > localhost.1023: Flags [.U], seq 6570371:6603138, ack 112, win 65401, urg 65535, options [nop,nop,TS val 2428568730 ecr 2428568716], length 32767
16:59:53.256330 IP localhost.1023 > localhost.login: Flags [.], ack 6603138, win 65535, options [nop,nop,TS val 2428568730 ecr 2428568716,nop,nop,sack 1 {6570371:6603138}], length 0
16:59:53.256351 IP localhost.login > localhost.1023: Flags [.U], seq 6603138:6635905, ack 112, win 65401, urg 65535, options [nop,nop,TS val 2428568730 ecr 2428568730], length 32767
16:59:53.256363 IP localhost.login > localhost.1023: Flags [.U], seq 6635905:6668672, ack 112, win 65401, urg 65535, options [nop,nop,TS val 2428568730 ecr 2428568730], length 32767
16:59:53.276255 IP localhost.login > localhost.1023: Flags [.U], seq 6635905:6668672, ack 112, win 65401, urg 65535, options [nop,nop,TS val 2428568750 ecr 2428568730], length 32767
16:59:53.300233 IP localhost.1023 > localhost.login: Flags [.], ack 6668672, win 65535, options [nop,nop,TS val 2428568774 ecr 2428568730,nop,nop,sack 1 {6635905:6668672}], length 0
16:59:53.300269 IP localhost.login > localhost.1023: Flags [.U], seq 6668672:6701439, ack 112, win 65401, urg 65535, options [nop,nop,TS val 2428568774 ecr 2428568774], length 32767
16:59:53.300281 IP localhost.login > localhost.1023: Flags [.U], seq 6701439:6734206, ack 112, win 65401, urg 65535, options [nop,nop,TS val 2428568774 ecr 2428568774], length 32767
```

## Ejercicio 5
Modifique la expresión de filtrado para capturar el tráfico telnet y haga un `telnet` entre las dos máquinas, usando la opción de visualización de negociación de opciones de telnet, es decir:
```
~> telnet
telnet> toggle options 
Will show option processing. 
telnet> open maquina
```
¿Qué opciones se están negociando entre las dos máquinas? ¿qué paquetes ve en el capturador que se están intercambiando?

## Ejercicio 6
Una vez dentro de la máquina remota, obtenga el prompt de telnet (para esto hay que pulsar `^]` y se consigue con la combinación de teclas `AltGr` + `Ctrl` + `]`). Use la opción `mode` de telnet para cambiar el modo de operación (haga un `man telnet` o `telnet> mode ?` para ver cómo se cambia el modo de operación). Haga "mode line" y observe la negociación de opciones. ¿En qué modo queda? Compruebe con tcpdump o wireshark si cuando escribe en el teclado del cliente, se mandan al servidor los caracteres según el modo de operación negociado.
Pruebe a hacer negociaciones "a mano" con telnet> send .... Intente negociar el modo semidúplex y observe lo que ocurre.
Siga ahora la negociación que se produce cuando se conecta a otros servidores telnet, por ejemplo a sdf.org o a telehack.com.
Modifique la expresión de filtrado para capturar el tráfico ssh y haga un ssh entre las dos máquinas. ¿Puede identificar que versión de SSH están utilizando cada uno de los extremos y que algoritmos de cifrado, resumen y compresión soportan cada uno? Una vez finalizada la fase de intercambio de claves de sesión y autenticVamos a analizar el tráfico de rlogin de una máquina a otra. Elija otra máquina del laboratorio a la que va a hacer rlogin. Lance tcpdump o wireshark con las opciones necesarias para que le muestre el tráfico que le interesa y nada más (es decir, sólo tráfico rlogin entre su máquina y la otra). Además quizás le convenga capturarlo en un fichero para analizarlo posteriormente con calma.
Haga rlogin a la otra máquina con /usr/bin/rlogin maquina (¡con el PATH completo!, si no el rlogin que ejecutará es en realidad un enlace a slogin, una versión que usa secure sockets, con cifrado, que viaja por otro puerto). Antes de introducir el password, compruebe que la secuencia de paquetes intercambiada se corresponde con la que se vio en teoría.
Introduzca su clave en rlogin y entre a la otra máquina. ¿Logra leer la clave con tcpdump? (use man ascii para ver el valor hexadecimal que corresponde).
Compruebe que en su directorio $HOME no existe ningún fichero .rhosts y pruebe a crear un fichero con el siguiente contenido: <hostname_cliente> <login_cliente> donde hostname_cliente es el nombre de la máquina en la que se encuentra conectado. ¿Que ocurre si intenta de nuevo realizar una conexión rlogin? Compruebe que el comportamiento es el mismo si en lugar de utilizar el hostname de la máquina utiliza su dirección IP. ¿Que tendría que escribir en ese fichero si quisiera darse acceso desde cualquier máquina?
Haga rlogin a otra máquina. Una vez dentro de la otra máquina, haga un cat de un fichero largo y haga ^S para parar la salida. Capture esto y observe qué ha pasado cuando ha hecho el ^S. Haga ahora ^C y observe lo que pasa.
Modifique la expresión de filtrado para capturar el tráfico telnet y haga un telnet entre las dos máquinas, usando la opción de visualización de negociación de opciones de telnet, es decir:
~> telnet
telnet> toggle options 
Will show option processing. 
telnet> open maquina
¿Qué opciones se están negociando entre las dos máquinas? ¿qué paquetes ve en el capturador que se están intercambiando?
Una vez dentro de la máquina remota, obtenga el prompt de telnet (para esto hay que pulsar ^] y se consigue con la combinación de teclas AltGr + Ctrl + "]"). Use la opción mode de telnet para cambiar el modo de operación (haga un "man telnet" o "telnet> mode ?" para ver cómo se cambia el modo de operación). Haga "mode line" y observe la negociación de opciones. ¿En qué modo queda? Compruebe con tcpdump o wireshark si cuando escribe en el teclado del cliente, se mandan al servidor los caracteres según el modo de operación negociado.
Pruebe a hacer negociaciones "a mano" con telnet> send .... Intente negociar el modo semidúplex y observe lo que ocurre.
Siga ahora la negociación que se produce cuando se conecta a otros servidores telnet, por ejemplo a sdf.org o a telehack.com.
Modifique la expresión de filtrado para capturar el tráfico ssh y haga un ssh entre las dos máquinas. ¿Puede identificar que versión de SSH están utilizando cada uno de los extremos y que algoritmos de cifrado, resumen y compresión soportan cada uno? Una vez finalizada la fase de intercambio de claves de sesión y autenticación de servidor ¿es capaz de ver el tráfico intercambiado entre ambas máquinas?
Vamos aprovechar la seguridad que nos proporciona SSH y la multiplexación de canales lógicos para realizar una sesión de TELNET segura con la máquina utilizada en los apartados anteriores. En concreto, vamos a utilizar el reenvío de puertos local. Para ello, tiene que crear una conexión SSH como hicimos en el apartado anterior esta vez añadiendo el parámetro -L e iniciar una sesión de telnet a localhost y al puerto que haya especificado en la conexión SSH. Compruebe que todo tráfico intercambiado en la sesión de telnet viaja por la conexión SSH y que el contenido va cifrado y por tanto no puede verlo.
Lance un tcpdump/wireshark que capture el tráfico ftp, tanto de datos como de control. Haga un ftp a otra máquina del laboratorio usando su IPv4, que puede obtener haciendo una consulta DNS del RR de tipo A (dig -t A). Por ejemplo, si queremos conectarnos a monitor01 haremos ftp -d 163.117.144.156 (la opción -d la ponemos para que muestre los comandos enviados).

Introduzca su nombre de usuario y contraseña. ¿Qué comandos envía el cliente? ¿qué respuestas recibe del servidor? ¿Es capaz de ver con el tcpdump los comandos y respuestas que se están intercambiando? ¿puede ver la clave en claro? Nota: Para entender el significado de alguno de los comandos que ve consulte las RFCs 2228 y 4217.
Liste el directorio y haga un get de un fichero. ¿Observa con tcpdump la apertura y el cierre de conexiones? Observe el sentido en el que se abren las conexiones y los puertos, y compárelos con los comandos PORT observados.
Vamos a conectarnos ahora usando la dirección IPv6 de las máquinas (recuerde que puede obtenerla con una consulta DNS de tipo AAAA), vamos a ver que el comportamiento es un poco distinto. Conéctese por IPv6 y haga un listado de fichero. ¿Qué comando observa en lugar del PORT?.
Con el programa telnet puede hacer conexiones TCP a puertos distintos que el del servicio telnet (el 23). Se puede hacer de dos maneras:
En un sólo comando: telnet máquina puerto.
O bien entrando primero en el programa telnet y luego, en el prompt, indicando el nombre de la máquina y nº de puerto. Esto nos permite indicar además otras opciones. Por ejemplo, si queremos ver la negociación de opciones: 
~>telnet 
telnet> toggle options 
Will show option processing. 
telnet> open máquina puerto
Para esta práctica nosotros lo haremos de esta segunda manera y así podremos observar la negociación de opciones. Haga:
~>telnet 
telnet> toggle options
A continuación pruebe lo siguiente: indique sólo el nombre de máquina (la máquina puede ser cualquiera de los ordenadores del laboratorio: vit001-vit140, jbit101-jbit156, it001-it031 ó doc001-doc031):
telnet> open máquina
Se conectará por defecto al puerto de telnet (el 23).
¿Quién inicia la negociación de opciones, el cliente o el servidor?
Ahora haga lo mismo, pero poniendo open maquina 23. ¿Se comporta igual?
Ahora conéctese al puerto TCP correspondiente a otro servicio, por ejemplo al puerto de control de FTP (el 21). ¿Intenta el cliente telnet hacer negociación de opciones con el servidor FTP?
Intercambie con el servidor de FTP los comandos que estudiamos en teoría. ¿Qué pasa si intenta listar los ficheros del servidor (LIST), o traerse (RETR) o llevarse (STOR) un fichero?
Pruebe ahora a hacer el LIST, RETR y STOR con la ayuda del comando PASV o EPSV. ¿Puede?ación de servidor ¿es capaz de ver el tráfico intercambiado entre ambas máquinas?
Vamos aprovechar la seguridad que nos proporciona SSH y la multiplexación de canales lógicos para realizar una sesión de TELNET segura con la máquina utilizada en los apartados anteriores. En concreto, vamos a utilizar el reenvío de puertos local. Para ello, tiene que crear una conexión SSH como hicimos en el apartado anterior esta vez añadiendo el parámetro -L e iniciar una sesión de telnet a localhost y al puerto que haya especificado en la conexión SSH. Compruebe que todo tráfico intercambiado en la sesión de telnet viaja por la conexión SSH y que el contenido va cifrado y por tanto no puede verlo.
Lance un tcpdump/wireshark que capture el tráfico ftp, tanto de datos como de control. Haga un ftp a otra máquina del laboratorio usando su IPv4, que puede obtener haciendo una consulta DNS del RR de tipo A (dig -t A). Por ejemplo, si queremos conectarnos a monitor01 haremos ftp -d 163.117.144.156 (la opción -d la ponemos para que muestre los comandos enviados).

Introduzca su nombre de usuario y contraseña. ¿Qué comandos envía el cliente? ¿qué respuestas recibe del servidor? ¿Es capaz de ver con el tcpdump los comandos y respuestas que se están intercambiando? ¿puede ver la clave en claro? Nota: Para entender el significado de alguno de los comandos que ve consulte las RFCs 2228 y 4217.
Liste el directorio y haga un get de un fichero. ¿Observa con tcpdump la apertura y el cierre de conexiones? Observe el sentido en el que se abren las conexiones y los puertos, y compárelos con los comandos PORT observados.
Vamos a conectarnos ahora usando la dirección IPv6 de las máquinas (recuerde que puede obtenerla con una consulta DNS de tipo AAAA), vamos a ver que el comportamiento es un poco distinto. Conéctese por IPv6 y haga un listado de fichero. ¿Qué comando observa en lugar del PORT?.
Con el programa telnet puede hacer conexiones TCP a puertos distintos que el del servicio telnet (el 23). Se puede hacer de dos maneras:
En un sólo comando: telnet máquina puerto.
O bien entrando primero en el programa telnet y luego, en el prompt, indicando el nombre de la máquina y nº de puerto. Esto nos permite indicar además otras opciones. Por ejemplo, si queremos ver la negociación de opciones: 
~>telnet 
telnet> toggle options 
Will show option processing. 
telnet> open máquina puerto
Para esta práctica nosotros lo haremos de esta segunda manera y así podremos observar la negociación de opciones. Haga:
~>telnet 
telnet> toggle options
A continuación pruebe lo siguiente: indique sólo el nombre de máquina (la máquina puede ser cualquiera de los ordenadores del laboratorio: vit001-vit140, jbit101-jbit156, it001-it031 ó doc001-doc031):
telnet> open máquina
Se conectará por defecto al puerto de telnet (el 23).
¿Quién inicia la negociación de opciones, el cliente o el servidor?
Ahora haga lo mismo, pero poniendo open maquina 23. ¿Se comporta igual?
Ahora conéctese al puerto TCP correspondiente a otro servicio, por ejemplo al puerto de control de FTP (el 21). ¿Intenta el cliente telnet hacer negociación de opciones con el servidor FTP?
Intercambie con el servidor de FTP los comandos que estudiamos en teoría. ¿Qué pasa si intenta listar los ficheros del servidor (LIST), o traerse (RETR) o llevarse (STOR) un fichero?
Pruebe ahora a hacer el LIST, RETR y STOR con la ayuda del comando PASV o EPSV. ¿Puede?
