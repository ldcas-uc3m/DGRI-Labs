# Práctica de rlogin, Telnet, y FTP

## Rlogin

### Ejercicio 1
Vamos a analizar el tráfico de rlogin de una máquina a otra. Elija otra máquina del laboratorio a la que va a hacer rlogin.

> [!NOTE]
> Alternativamente, puedes hacer rlogin a la misma máquina

1. Lance `tcpdump` o wireshark con las opciones necesarias para que le muestre el tráfico que le interesa y nada más (es decir, sólo tráfico rlogin entre su máquina y la otra). Además quizás le convenga capturarlo en un fichero para analizarlo posteriormente con calma.
```
tcpdump -w dumpfile.pcap -i lo tcp port 513 &
```
> [!NOTE]
> Dado que estamos haciendo rlogin a nosotros mismos, usamos la interfaz de loopback, `lo`.  
> Para ver las interfaces disponibles, use `$ ip link show`

> [!NOTE]
> También puedes filtrar por el host de origen, con `src x.x.x.x`, pero recuerda que para unir filtros hay que usar `and`, e.g. `$ tcpdump tcp port 22 and src 163.117.171.191`

1. Haga rlogin a la otra máquina con `/usr/bin/rlogin <maquina>` (¡con el PATH completo!, si no el `rlogin` que ejecutará es en realidad un enlace a `slogin`, una versión que usa _secure sockets_, con cifrado, que viaja por otro puerto). Antes de introducir el _password_, compruebe que la secuencia de paquetes intercambiada se corresponde con la que se vio en teoría.

```
/usr/bin/rlogin 127.0.0.1
```

> [!NOTE]
> Para salir, `Ctrl`+`b`, trae a _foreground_ tcpdump con `fg`, y páralo con `Ctrl`+`c`


### Ejercicio 2
Introduzca su clave en rlogin y entre a la otra máquina. ¿Logra leer la clave con `tcpdump` (use `man ascii` para ver el valor hexadecimal que corresponde).

```
tcpdump -r dumpfile.pcap -X
```
> Viene un paquete por caracter.
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

### Ejercicio 3
Compruebe que en su directorio `$HOME` (del servidor) no existe ningún fichero `.rhosts` y pruebe a crear un fichero con el siguiente contenido: `<hostname_cliente> <login_cliente>` donde `hostname_cliente` es el nombre de la máquina en la que se encuentra conectado, y `login_cliente` el nombre de usuario.
1. ¿Qué ocurre si intenta de nuevo realizar una conexión rlogin? Compruebe que el comportamiento es el mismo si en lugar de utilizar el hostname de la máquina utiliza su dirección IP.  
> No pide credenciales.
2. ¿Qué tendría que escribir en ese fichero si quisiera darse acceso desde cualquier máquina?  
> Poner `+ <login_cliente>`.

### Ejercicio 4
Haga `rlogin` a otra máquina. Una vez dentro de la otra máquina, haga un `cat` de un fichero largo y haga `^S` para parar la salida. Capture esto y observe qué ha pasado cuando ha hecho el `^S`. Haga ahora `^C` y observe lo que pasa.  
> Se envían los paquetes con _flag_ URG.

> [!NOTE]
> Para sacar basura por pantalla: `base64 /dev/urandom`
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


## Telnet

### Ejercicio 5
Modifique la expresión de filtrado para capturar el tráfico telnet y haga un `telnet` entre las dos máquinas, usando la opción de visualización de negociación de opciones de telnet, es decir:
```
$ telnet
telnet> toggle options
Will show option processing.
telnet> open <maquina>
```
¿Qué opciones se están negociando entre las dos máquinas? ¿Qué paquetes ve en el capturador que se están intercambiando?
```
Trying 127.0.0.1...
Connected to 127.0.0.1.
Escape character is '^]'.
SENT DO ENCRYPT
SENT WILL ENCRYPT
SENT DO SUPPRESS GO AHEAD
SENT WILL TERMINAL TYPE
SENT WILL NAWS
SENT WILL TSPEED
SENT WILL LFLOW
SENT WILL LINEMODE
SENT WILL NEW-ENVIRON
SENT DO STATUS
RCVD DO NEW-ENVIRON
RCVD WONT ENCRYPT
RCVD DONT ENCRYPT
RCVD WILL SUPPRESS GO AHEAD
RCVD DO TERMINAL TYPE
RCVD DO NAWS
SENT IAC SB NAWS 0 120 (120) 0 30 (30)
RCVD DO TSPEED
RCVD DO LFLOW
RCVD DONT LINEMODE
RCVD WILL STATUS
RCVD IAC SB NEW-ENVIRON SEND
SENT IAC SB NEW-ENVIRON IS
RCVD DO AUTHENTICATION
SENT WONT AUTHENTICATION
RCVD DO XDISPLOC
SENT WONT XDISPLOC
RCVD IAC SB TERMINAL-SPEED SEND
SENT IAC SB TERMINAL-SPEED IS 9600,9600
RCVD IAC SB NEW-ENVIRON SEND
SENT IAC SB NEW-ENVIRON IS
RCVD IAC SB TERMINAL-TYPE SEND
SENT IAC SB TERMINAL-TYPE IS "XTERM-256COLOR"
RCVD DO ECHO
SENT WONT ECHO
RCVD WILL ECHO
SENT DO ECHO
Debian GNU/Linux 12
monitor01 login:
```


### Ejercicio 6
Una vez dentro de la máquina remota, obtenga el prompt de telnet (para esto hay que pulsar `^]` (`Ctrl` + `]`). Use la opción `mode` de telnet para cambiar el modo de operación. Haga "mode line" y observe la negociación de opciones. ¿En qué modo queda?
> [!NOTE]
> Haga un `man telnet` o `telnet> mode ?` para ver cómo se cambia el modo de operación.

> Me quedo en el que estaba, ya que el servidor lo ha rechazado
```
telnet> mode line
SENT DONT SUPPRESS GO AHEAD
SENT WILL LINEMODE
SENT DONT ECHO
RCVD WONT SUPPRESS GO AHEAD
RCVD WILL SUPPRESS GO AHEAD
SENT DO SUPPRESS GO AHEAD
RCVD DONT LINEMODE
RCVD WONT ECHO
```

Compruebe con `tcpdump` si cuando escribe en el teclado del cliente, se mandan al servidor los caracteres según el modo de operación negociado.

```
tcpdump -i lo tcp port 23 -X
```

```
18:33:40.673137 IP localhost.42352 > localhost.telnet: Flags [P.], seq 1680008630:1680008631, ack 996311638, win 65442, options [nop,nop,TS val 1344700981 ecr 1344695606], length 1
        0x0000:  4500 0035 9faa 4000 4006 9d16 7f00 0001  E..5..@.@.......
        0x0010:  7f00 0001 a570 0017 6422 e5b6 3b62 8256  .....p..d"..;b.V
        0x0020:  8018 ffa2 fe29 0000 0101 080a 5026 8235  .....)......P&.5
        0x0030:  5026 6d36 30                             P&m60
18:33:40.673343 IP localhost.telnet > localhost.42352: Flags [P.], seq 1:2, ack 1, win 65450, options [nop,nop,TS val 1344700982 ecr 1344700981], length 1
        0x0000:  4510 0035 7c79 4000 4006 c037 7f00 0001  E..5|y@.@..7....
        0x0010:  7f00 0001 0017 a570 3b62 8256 6422 e5b7  .......p;b.Vd"..
        0x0020:  8018 ffaa fe29 0000 0101 080a 5026 8236  .....)......P&.6
        0x0030:  5026 8235 30                             P&.50
18:33:40.673358 IP localhost.42352 > localhost.telnet: Flags [.], ack 2, win 65441, options [nop,nop,TS val 1344700982 ecr 1344700982], length 0
        0x0000:  4500 0034 9fab 4000 4006 9d16 7f00 0001  E..4..@.@.......
        0x0010:  7f00 0001 a570 0017 6422 e5b7 3b62 8257  .....p..d"..;b.W
        0x0020:  8010 ffa1 fe28 0000 0101 080a 5026 8236  .....(......P&.6
        0x0030:  5026 8236                                P&.6
18:33:42.067346 IP localhost.42352 > localhost.telnet: Flags [P.], seq 1:2, ack 2, win 65441, options [nop,nop,TS val 1344702376 ecr 1344700982], length 1
        0x0000:  4500 0035 9fac 4000 4006 9d14 7f00 0001  E..5..@.@.......
        0x0010:  7f00 0001 a570 0017 6422 e5b7 3b62 8257  .....p..d"..;b.W
        0x0020:  8018 ffa1 fe29 0000 0101 080a 5026 87a8  .....)......P&..
        0x0030:  5026 8236 34                             P&.64
18:33:42.067567 IP localhost.telnet > localhost.42352: Flags [P.], seq 2:3, ack 2, win 65450, options [nop,nop,TS val 1344702376 ecr 1344702376], length 1
        0x0000:  4510 0035 7c7a 4000 4006 c036 7f00 0001  E..5|z@.@..6....
        0x0010:  7f00 0001 0017 a570 3b62 8257 6422 e5b8  .......p;b.Wd"..
        0x0020:  8018 ffaa fe29 0000 0101 080a 5026 87a8  .....)......P&..
        0x0030:  5026 87a8 34                             P&..4
18:33:42.067595 IP localhost.42352 > localhost.telnet: Flags [.], ack 3, win 65440, options [nop,nop,TS val 1344702376 ecr 1344702376], length 0
        0x0000:  4500 0034 9fad 4000 4006 9d14 7f00 0001  E..4..@.@.......
        0x0010:  7f00 0001 a570 0017 6422 e5b8 3b62 8258  .....p..d"..;b.X
        0x0020:  8010 ffa0 fe28 0000 0101 080a 5026 87a8  .....(......P&..
        0x0030:  5026 87a8                                P&..
18:33:42.396561 IP localhost.42352 > localhost.telnet: Flags [P.], seq 2:3, ack 3, win 65440, options [nop,nop,TS val 1344702705 ecr 1344702376], length 1
        0x0000:  4500 0035 9fae 4000 4006 9d12 7f00 0001  E..5..@.@.......
        0x0010:  7f00 0001 a570 0017 6422 e5b8 3b62 8258  .....p..d"..;b.X
        0x0020:  8018 ffa0 fe29 0000 0101 080a 5026 88f1  .....)......P&..
        0x0030:  5026 87a8 32                             P&..2
18:33:42.396789 IP localhost.telnet > localhost.42352: Flags [P.], seq 3:4, ack 3, win 65450, options [nop,nop,TS val 1344702705 ecr 1344702705], length 1
        0x0000:  4510 0035 7c7b 4000 4006 c035 7f00 0001  E..5|{@.@..5....
        0x0010:  7f00 0001 0017 a570 3b62 8258 6422 e5b9  .......p;b.Xd"..
        0x0020:  8018 ffaa fe29 0000 0101 080a 5026 88f1  .....)......P&..
        0x0030:  5026 88f1 32                             P&..2
```


### Ejercicio 7
Pruebe a hacer negociaciones "a mano" con `telnet> send ...`. Intente negociar el modo semidúplex y observe lo que ocurre.


### Ejercicio 8
Siga ahora la negociación que se produce cuando se conecta a otros servidores telnet, por ejemplo a `sdf.org` o a `telehack.com`.
```
telnet> open sdf.org
Trying 205.166.94.16...
Connected to sdf.org.
Escape character is '^]'.
SENT DO ENCRYPT
SENT WILL ENCRYPT
SENT DO SUPPRESS GO AHEAD
SENT WILL TERMINAL TYPE
SENT WILL NAWS
SENT WILL TSPEED
SENT WILL LFLOW
SENT WILL LINEMODE
SENT WILL NEW-ENVIRON
SENT DO STATUS
RCVD DO AUTHENTICATION
SENT WONT AUTHENTICATION
RCVD WILL ENCRYPT
SENT IAC SB ENCRYPT SUPPORT
RCVD DO ENCRYPT
RCVD IAC SB ENCRYPT SUPPORT DES_CFB64 DES_OFB64
RCVD WILL SUPPRESS GO AHEAD
RCVD DO TERMINAL TYPE
RCVD DO NAWS
SENT IAC SB NAWS 0 95 (95) 0 23 (23)
RCVD DO TSPEED
RCVD DO LFLOW
RCVD DO LINEMODE
SENT IAC SB LINEMODE SLC SYNCH NOSUPPORT 0; IP VARIABLE|FLUSHIN|FLUSHOUT 3; AO VARIABLE 15; AYT NOSUPPORT 0; ABORT VARIABLE|FLUSHIN|FLUSHOUT 28; EOF VARIABLE 4; SUSP VARIABLE|FLUSHIN 26; EC VARIABLE 127; EL VARIABLE 21; EW VARIABLE 23; RP VARIABLE 18; LNEXT VARIABLE 22; XON VARIABLE 17; XOFF VARIABLE 19; FORW1 NOSUPPORT 0; FORW2 NOSUPPORT 0;
SENT DO SUPPRESS GO AHEAD
RCVD IAC SB LINEMODE MODE EDIT|TRAPSIG|SOFT_TAB
SENT IAC SB LINEMODE MODE EDIT|TRAPSIG|SOFT_TAB|ACK
RCVD DO NEW-ENVIRON
RCVD WILL STATUS
RCVD DO XDISPLOC
SENT WONT XDISPLOC
RCVD DO OLD-ENVIRON
SENT WONT OLD-ENVIRON
RCVD IAC SB TERMINAL-SPEED SEND
SENT IAC SB TERMINAL-SPEED IS 9600,9600
RCVD IAC SB NEW-ENVIRON SEND
SENT IAC SB NEW-ENVIRON IS
RCVD IAC SB TERMINAL-TYPE SEND
SENT IAC SB TERMINAL-TYPE IS "XTERM-256COLOR"
RCVD DO ECHO
SENT WONT ECHO
RCVD WILL ECHO
SENT DO ECHO
RCVD DO BINARY
SENT WILL BINARY
RCVD IAC SB TOGGLE-FLOW-CONTROL RESTART-ANY
RCVD WONT ECHO
SENT DONT ECHO
RCVD IAC SB LINEMODE SLC AYT NOSUPPORT|ACK 0; FORW1 NOSUPPORT|ACK 0; FORW2 NOSUPPORT|ACK 0;

sdf.org (pts/195)
if new, login 'new' ..

login:
```



## SSH

### Ejercicio 9
Modifique la expresión de filtrado para capturar el tráfico SSH (puerto `22`) y haga un SSH entre las dos máquinas. ¿Puede identificar que versión de SSH están utilizando cada uno de los extremos y que algoritmos de cifrado, resumen y compresión soportan cada uno?

```
18:59:52.740109 IP vit001.lab.it.uc3m.es.38402 > vit002.ssh: Flags [P.], seq 0:32, ack 1, win 64240, options [nop,nop,TS val 3436912964 ecr 1046413007], length 32: SSH: SSH-2.0-OpenSSH_9.2p1 Debian-2
        0x0000:  4510 0054 6589 4000 4006 36a0 a375 abbf  E..Te.@.@.6..u..
        0x0010:  a375 abc0 9602 0016 5b6b 87ea a43a 8b0f  .u......[k...:..
        0x0020:  8018 faf0 db8d 0000 0101 080a ccdb 2144  ..............!D
        0x0030:  3e5e fecf 5353 482d 322e 302d 4f70 656e  >^..SSH-2.0-Open
        0x0040:  5353 485f 392e 3270 3120 4465 6269 616e  SSH_9.2p1.Debian
        0x0050:  2d32 0d0a                                -2..

...

18:59:52.755284 IP vit001.lab.it.uc3m.es.38402 > vit002.ssh: Flags [.], seq 32:1536, ack 1105, win 63248, options [nop,nop,TS val 3436912979 ecr 1046413023], length 1504
        0x0000:  4510 0614 658c 4000 4006 30dd a375 abbf  E...e.@.@.0..u..
        0x0010:  a375 abc0 9602 0016 5b6b 880a a43a 8f5f  .u......[k...:._
        0x0020:  8010 f710 a471 0000 0101 080a ccdb 2153  .....q........!S
        0x0030:  3e5e fedf 0000 05dc 0414 11ce 5f76 ed02  >^.........._v..
        0x0040:  92a3 f534 cc40 e9fb 9fd9 0000 0114 736e  ...4.@........sn
        0x0050:  7472 7570 3736 3178 3235 3531 392d 7368  trup761x25519-sh
        0x0060:  6135 3132 406f 7065 6e73 7368 2e63 6f6d  a512@openssh.com
        0x0070:  2c63 7572 7665 3235 3531 392d 7368 6132  ,curve25519-sha2
        0x0080:  3536 2c63 7572 7665 3235 3531 392d 7368  56,curve25519-sh
        0x0090:  6132 3536 406c 6962 7373 682e 6f72 672c  a256@libssh.org,
        0x00a0:  6563 6468 2d73 6861 322d 6e69 7374 7032  ecdh-sha2-nistp2
        0x00b0:  3536 2c65 6364 682d 7368 6132 2d6e 6973  56,ecdh-sha2-nis
        0x00c0:  7470 3338 342c 6563 6468 2d73 6861 322d  tp384,ecdh-sha2-
        0x00d0:  6e69 7374 7035 3231 2c64 6966 6669 652d  nistp521,diffie-
        0x00e0:  6865 6c6c 6d61 6e2d 6772 6f75 702d 6578  hellman-group-ex
        0x00f0:  6368 616e 6765 2d73 6861 3235 362c 6469  change-sha256,di
        0x0100:  6666 6965 2d68 656c 6c6d 616e 2d67 726f  ffie-hellman-gro
        0x0110:  7570 3136 2d73 6861 3531 322c 6469 6666  up16-sha512,diff
        0x0120:  6965 2d68 656c 6c6d 616e 2d67 726f 7570  ie-hellman-group
        0x0130:  3138 2d73 6861 3531 322c 6469 6666 6965  18-sha512,diffie
        0x0140:  2d68 656c 6c6d 616e 2d67 726f 7570 3134  -hellman-group14
        0x0150:  2d73 6861 3235 362c 6578 742d 696e 666f  -sha256,ext-info
        0x0160:  2d63 0000 01cf 6563 6473 612d 7368 6132  -c....ecdsa-sha2
        0x0170:  2d6e 6973 7470 3235 362d 6365 7274 2d76  -nistp256-cert-v
        0x0180:  3031 406f 7065 6e73 7368 2e63 6f6d 2c72  01@openssh.com,r
        0x0190:  7361 2d73 6861 322d 3531 322d 6365 7274  sa-sha2-512-cert
        0x01a0:  2d76 3031 406f 7065 6e73 7368 2e63 6f6d  -v01@openssh.com
        0x01b0:  2c72 7361 2d73 6861 322d 3235 362d 6365  ,rsa-sha2-256-ce
        0x01c0:  7274 2d76 3031 406f 7065 6e73 7368 2e63  rt-v01@openssh.c
        0x01d0:  6f6d 2c65 6364 7361 2d73 6861 322d 6e69  om,ecdsa-sha2-ni
        0x01e0:  7374 7032 3536 2c72 7361 2d73 6861 322d  stp256,rsa-sha2-
        0x01f0:  3531 322c 7273 612d 7368 6132 2d32 3536  512,rsa-sha2-256
        0x0200:  2c73 7368 2d65 6432 3535 3139 2d63 6572  ,ssh-ed25519-cer
        0x0210:  742d 7630 3140 6f70 656e 7373 682e 636f  t-v01@openssh.co
        0x0220:  6d2c 6563 6473 612d 7368 6132 2d6e 6973  m,ecdsa-sha2-nis
        0x0230:  7470 3338 342d 6365 7274 2d76 3031 406f  tp384-cert-v01@o
        0x0240:  7065 6e73 7368 2e63 6f6d 2c65 6364 7361  penssh.com,ecdsa
        0x0250:  2d73 6861 322d 6e69 7374 7035 3231 2d63  -sha2-nistp521-c
        0x0260:  6572 742d 7630 3140 6f70 656e 7373 682e  ert-v01@openssh.
        0x0270:  636f 6d2c 736b 2d73 7368 2d65 6432 3535  com,sk-ssh-ed255
        0x0280:  3139 2d63 6572 742d 7630 3140 6f70 656e  19-cert-v01@open
        0x0290:  7373 682e 636f 6d2c 736b 2d65 6364 7361  ssh.com,sk-ecdsa
        0x02a0:  2d73 6861 322d 6e69 7374 7032 3536 2d63  -sha2-nistp256-c
        0x02b0:  6572 742d 7630 3140 6f70 656e 7373 682e  ert-v01@openssh.
        0x02c0:  636f 6d2c 7373 682d 6564 3235 3531 392c  com,ssh-ed25519,
        0x02d0:  6563 6473 612d 7368 6132 2d6e 6973 7470  ecdsa-sha2-nistp
        0x02e0:  3338 342c 6563 6473 612d 7368 6132 2d6e  384,ecdsa-sha2-n
        0x02f0:  6973 7470 3532 312c 736b 2d73 7368 2d65  istp521,sk-ssh-e
        0x0300:  6432 3535 3139 406f 7065 6e73 7368 2e63  d25519@openssh.c
        0x0310:  6f6d 2c73 6b2d 6563 6473 612d 7368 6132  om,sk-ecdsa-sha2
        0x0320:  2d6e 6973 7470 3235 3640 6f70 656e 7373  -nistp256@openss
        0x0330:  682e 636f 6d00 0000 6c63 6861 6368 6132  h.com...lchacha2
        0x0340:  302d 706f 6c79 3133 3035 406f 7065 6e73  0-poly1305@opens
        0x0350:  7368 2e63 6f6d 2c61 6573 3132 382d 6374  sh.com,aes128-ct
        0x0360:  722c 6165 7331 3932 2d63 7472 2c61 6573  r,aes192-ctr,aes
        0x0370:  3235 362d 6374 722c 6165 7331 3238 2d67  256-ctr,aes128-g
        0x0380:  636d 406f 7065 6e73 7368 2e63 6f6d 2c61  cm@openssh.com,a
        0x0390:  6573 3235 362d 6763 6d40 6f70 656e 7373  es256-gcm@openss
        0x03a0:  682e 636f 6d00 0000 6c63 6861 6368 6132  h.com...lchacha2
        0x03b0:  302d 706f 6c79 3133 3035 406f 7065 6e73  0-poly1305@opens
        0x03c0:  7368 2e63 6f6d 2c61 6573 3132 382d 6374  sh.com,aes128-ct
        0x03d0:  722c 6165 7331 3932 2d63 7472 2c61 6573  r,aes192-ctr,aes
        0x03e0:  3235 362d 6374 722c 6165 7331 3238 2d67  256-ctr,aes128-g
        0x03f0:  636d 406f 7065 6e73 7368 2e63 6f6d 2c61  cm@openssh.com,a
        0x0400:  6573 3235 362d 6763 6d40 6f70 656e 7373  es256-gcm@openss
        0x0410:  682e 636f 6d00 0000 d575 6d61 632d 3634  h.com....umac-64
        0x0420:  2d65 746d 406f 7065 6e73 7368 2e63 6f6d  -etm@openssh.com
        0x0430:  2c75 6d61 632d 3132 382d 6574 6d40 6f70  ,umac-128-etm@op
        0x0440:  656e 7373 682e 636f 6d2c 686d 6163 2d73  enssh.com,hmac-s
        0x0450:  6861 322d 3235 362d 6574 6d40 6f70 656e  ha2-256-etm@open
        0x0460:  7373 682e 636f 6d2c 686d 6163 2d73 6861  ssh.com,hmac-sha
        0x0470:  322d 3531 322d 6574 6d40 6f70 656e 7373  2-512-etm@openss
        0x0480:  682e 636f 6d2c 686d 6163 2d73 6861 312d  h.com,hmac-sha1-
        0x0490:  6574 6d40 6f70 656e 7373 682e 636f 6d2c  etm@openssh.com,
        0x04a0:  756d 6163 2d36 3440 6f70 656e 7373 682e  umac-64@openssh.
        0x04b0:  636f 6d2c 756d 6163 2d31 3238 406f 7065  com,umac-128@ope
        0x04c0:  6e73 7368 2e63 6f6d 2c68 6d61 632d 7368  nssh.com,hmac-sh
        0x04d0:  6132 2d32 3536 2c68 6d61 632d 7368 6132  a2-256,hmac-sha2
        0x04e0:  2d35 3132 2c68 6d61 632d 7368 6131 0000  -512,hmac-sha1..
        0x04f0:  00d5 756d 6163 2d36 342d 6574 6d40 6f70  ..umac-64-etm@op
        0x0500:  656e 7373 682e 636f 6d2c 756d 6163 2d31  enssh.com,umac-1
        0x0510:  3238 2d65 746d 406f 7065 6e73 7368 2e63  28-etm@openssh.c
        0x0520:  6f6d 2c68 6d61 632d 7368 6132 2d32 3536  om,hmac-sha2-256
        0x0530:  2d65 746d 406f 7065 6e73 7368 2e63 6f6d  -etm@openssh.com
        0x0540:  2c68 6d61 632d 7368 6132 2d35 3132 2d65  ,hmac-sha2-512-e
        0x0550:  746d 406f 7065 6e73 7368 2e63 6f6d 2c68  tm@openssh.com,h
        0x0560:  6d61 632d 7368 6131 2d65 746d 406f 7065  mac-sha1-etm@ope
        0x0570:  6e73 7368 2e63 6f6d 2c75 6d61 632d 3634  nssh.com,umac-64
        0x0580:  406f 7065 6e73 7368 2e63 6f6d 2c75 6d61  @openssh.com,uma
        0x0590:  632d 3132 3840 6f70 656e 7373 682e 636f  c-128@openssh.co
        0x05a0:  6d2c 686d 6163 2d73 6861 322d 3235 362c  m,hmac-sha2-256,
        0x05b0:  686d 6163 2d73 6861 322d 3531 322c 686d  hmac-sha2-512,hm
        0x05c0:  6163 2d73 6861 3100 0000 1a6e 6f6e 652c  ac-sha1....none,
        0x05d0:  7a6c 6962 406f 7065 6e73 7368 2e63 6f6d  zlib@openssh.com
        0x05e0:  2c7a 6c69 6200 0000 1a6e 6f6e 652c 7a6c  ,zlib....none,zl
        0x05f0:  6962 406f 7065 6e73 7368 2e63 6f6d 2c7a  ib@openssh.com,z
        0x0600:  6c69 6200 0000 0000 0000 0000 0000 0000  lib.............
        0x0610:  0000 0000                                ....                            -2..
```


### Ejercicio 10
Vamos a aprovechar la seguridad que nos proporciona SSH y la multiplexación de canales lógicos para realizar una sesión de TELNET segura con la máquina utilizada en los apartados anteriores. En concreto, vamos a utilizar el reenvío de puertos local. Para ello, tiene que crear una conexión SSH como hicimos en el apartado anterior esta vez añadiendo el parámetro `-L` e iniciar una sesión de telnet a `localhost` y al puerto que haya especificado en la conexión SSH. Compruebe que todo tráfico intercambiado en la sesión de telnet viaja por la conexión SSH y que el contenido va cifrado y, por tanto, no puede verlo.
```
ssh -L 2323:localhost:23 vit002
```
```
user@vit001:~ $ telnet
telnet> open localhost 2323
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
Debian GNU/Linux 12
vit002 login:
```

> Podemos comprobar con `tcpdump -i eth0 tcp port 23` que no pasa ningún paquete.



## FTP
Lance un `tcpdump` que capture el tráfico FTP, tanto de datos como de control. Haga un FTP a otra máquina del laboratorio usando su IPv4, que puede obtener haciendo una consulta DNS del RR de tipo A (`$ dig -t A`). Por ejemplo, si queremos conectarnos a `monitor01` haremos `ftp -d 163.117.144.156` (la opción `-d` la ponemos para que muestre los comandos enviados).

```
tcpdump port ftp or ftp-data
```

### Ejercicio 11
Introduzca su nombre de usuario y contraseña. ¿Qué comandos envía el cliente? ¿Qué respuestas recibe del servidor? ¿Es capaz de ver con el `tcpdump` los comandos y respuestas que se están intercambiando? ¿Puede ver la clave en claro?

El protocolo funciona correctamente:
```
Connected to 163.117.144.156.
220 (vsFTPd 3.0.3)
ftp: setsockopt: Bad file descriptor
Name (163.117.144.156:0429021): 0429021
---> AUTH TLS
530 Please login with USER and PASS.
---> AUTH SSL
530 Please login with USER and PASS.
SSL not available
---> USER 0429021
331 Please specify the password.
Password:
---> PASS XXXX
230 Login successful.
---> SYST
215 UNIX Type: L8
Remote system type is UNIX.
Using binary mode to transfer files.
ftp>
```

La traza es:
```
19:56:47.833911 IP vit001.lab.it.uc3m.es.38076 > monitor01.ftp: Flags [S], seq 91488278, win 64240, options [mss 1460,sackOK,TS val 1015969837 ecr 0], length 0
19:56:47.833991 IP monitor01.ftp > vit001.lab.it.uc3m.es.38076: Flags [S.], seq 2169422682, ack 91488279, win 65160, options [mss 1460,sackOK,TS val 3820931634 ecr 1015969837], length 0
19:56:47.834887 IP vit001.lab.it.uc3m.es.38076 > monitor01.ftp: Flags [.], ack 1, win 64240, options [nop,nop,TS val 1015969839 ecr 3820931634], length 0
19:56:47.842555 IP monitor01.ftp > vit001.lab.it.uc3m.es.38076: Flags [P.], seq 1:21, ack 1, win 65160, options [nop,nop,TS val 3820931643 ecr 1015969839], length 20: FTP: 220 (vsFTPd 3.0.3)
19:56:47.843739 IP vit001.lab.it.uc3m.es.38076 > monitor01.ftp: Flags [.], ack 21, win 64220, options [nop,nop,TS val 1015969848 ecr 3820931643], length 0
19:56:55.410184 IP vit001.lab.it.uc3m.es.38076 > monitor01.ftp: Flags [P.], seq 1:11, ack 21, win 64220, options [nop,nop,TS val 1015977414 ecr 3820931643], length 10: FTP: AUTH TLS
19:56:55.410221 IP monitor01.ftp > vit001.lab.it.uc3m.es.38076: Flags [.], ack 11, win 65150, options [nop,nop,TS val 3820939210 ecr 1015977414], length 0
19:56:55.410328 IP monitor01.ftp > vit001.lab.it.uc3m.es.38076: Flags [P.], seq 21:59, ack 11, win 65150, options [nop,nop,TS val 3820939211 ecr 1015977414], length 38: FTP: 530 Please login with USER and PASS.
19:56:55.411535 IP vit001.lab.it.uc3m.es.38076 > monitor01.ftp: Flags [.], ack 59, win 64182, options [nop,nop,TS val 1015977415 ecr 3820939211], length 0
19:56:55.411535 IP vit001.lab.it.uc3m.es.38076 > monitor01.ftp: Flags [P.], seq 11:21, ack 59, win 64182, options [nop,nop,TS val 1015977416 ecr 3820939211], length 10: FTP: AUTH SSL
19:56:55.411688 IP monitor01.ftp > vit001.lab.it.uc3m.es.38076: Flags [P.], seq 59:97, ack 21, win 65140, options [nop,nop,TS val 3820939212 ecr 1015977416], length 38: FTP: 530 Please login with USER and PASS.
19:56:55.412943 IP vit001.lab.it.uc3m.es.38076 > monitor01.ftp: Flags [P.], seq 21:35, ack 97, win 64144, options [nop,nop,TS val 1015977417 ecr 3820939212], length 14: FTP: USER 0429021
19:56:55.412973 IP monitor01.ftp > vit001.lab.it.uc3m.es.38076: Flags [P.], seq 97:131, ack 35, win 65126, options [nop,nop,TS val 3820939213 ecr 1015977417], length 34: FTP: 331 Please specify the password.
19:56:55.455046 IP vit001.lab.it.uc3m.es.38076 > monitor01.ftp: Flags [.], ack 131, win 64110, options [nop,nop,TS val 1015977459 ecr 3820939213], length 0
19:57:00.114870 IP vit001.lab.it.uc3m.es.38076 > monitor01.ftp: Flags [P.], seq 35:55, ack 131, win 64110, options [nop,nop,TS val 1015982119 ecr 3820939213], length 24: FTP: PASS MiSuperContraseña
19:57:00.157704 IP monitor01.ftp > vit001.lab.it.uc3m.es.38076: Flags [.], ack 55, win 65106, options [nop,nop,TS val 3820943958 ecr 1015982119], length 0
19:57:00.839447 IP monitor01.ftp > vit001.lab.it.uc3m.es.38076: Flags [P.], seq 131:154, ack 55, win 65106, options [nop,nop,TS val 3820944640 ecr 1015982119], length 23: FTP: 230 Login successful.
19:57:00.841020 IP vit001.lab.it.uc3m.es.38076 > monitor01.ftp: Flags [.], ack 154, win 64087, options [nop,nop,TS val 1015982845 ecr 3820944640], length 0
19:57:00.841020 IP vit001.lab.it.uc3m.es.38076 > monitor01.ftp: Flags [P.], seq 55:61, ack 154, win 64087, options [nop,nop,TS val 1015982845 ecr 3820944640], length 6: FTP: SYST
19:57:00.841077 IP monitor01.ftp > vit001.lab.it.uc3m.es.38076: Flags [.], ack 61, win 65100, options [nop,nop,TS val 3820944641 ecr 1015982845], length 0
19:57:00.841198 IP monitor01.ftp > vit001.lab.it.uc3m.es.38076: Flags [P.], seq 154:173, ack 61, win 65100, options [nop,nop,TS val 3820944641 ecr 1015982845], length 19: FTP: 215 UNIX Type: L8
19:57:00.887030 IP vit001.lab.it.uc3m.es.38076 > monitor01.ftp: Flags [.], ack 173, win 64087, options [nop,nop,TS val 1015982891 ecr 3820944641], length 0
```

> La contraseña se ve en claro

> [!NOTE]
> Para entender el significado de alguno de los comandos que ve consulte las RFCs 2228 y 4217.


### Ejercicio 12
Liste el directorio (`ls`) y haga un `get` de un fichero. ¿Observa con `tcpdump` la apertura y el cierre de conexiones? Observe el sentido en el que se abren las conexiones y los puertos, y compárelos con los comandos PORT observados.
```
ftp> get dumpfile.pcap
local: dumpfile.pcap remote: dumpfile.pcap
---> TYPE I
200 Switching to Binary mode.
ftp: setsockopt (ignored): Permission denied
---> PORT 163,117,171,191,140,151
200 PORT command successful. Consider using PASV.
---> RETR dumpfile.pcap
150 Opening BINARY mode data connection for dumpfile.pcap (24 bytes).
226 Transfer complete.
24 bytes received in 0.00 secs (18.9777 kB/s)
```


### Ejercicio 13
Vamos a conectarnos ahora usando la dirección IPv6 de las máquinas (recuerde que puede obtenerla con una consulta DNS de tipo AAAA, o con `$ ip a`), vamos a ver que el comportamiento es un poco distinto. Conéctese por IPv6 y haga un listado de fichero. ¿Qué comando observa en lugar del PORT?.
```
ftp -d 2001:720:410:100c::156
```
```
ftp> ls
ftp: setsockopt (ignored): Permission denied
---> EPRT |2|2001:720:410:1009::191|57853|
200 EPRT command successful. Consider using EPSV.
---> LIST
150 Here comes the directory listing.
...
```
> Usa EPRT


### Ejercicio 14
Con el programa telnet puede hacer conexiones TCP a puertos distintos que el del servicio telnet (el `23`). Se puede hacer de dos maneras:
- En un sólo comando: `$ telnet <máquina> <puerto>`.
- O bien entrando primero en el programa `telnet` y luego, en el prompt, indicando el nombre de la máquina y nº de puerto. Esto nos permite indicar además otras opciones. Por ejemplo, si queremos ver la negociación de opciones:
	```
	$ telnet
	telnet> toggle options
	Will show option processing.
	telnet> open <máquina> <puerto>
	```
Para esta práctica nosotros lo haremos de esta segunda manera y así podremos observar la negociación de opciones. Haga:
```
$ telnet
telnet> toggle options
```
A continuación pruebe lo siguiente: indique sólo el nombre de máquina (la máquina puede ser cualquiera de los ordenadores del laboratorio: `vit001`-`vit140`, `jbit101`-`jbit156`, `it001`-`it031` ó `doc001`-`doc031`):
```
telnet> open <máquina>
```
Se conectará por defecto al puerto de telnet (el `23`).

```
telnet> open vit002
Trying 163.117.171.192...
Connected to vit002.
Escape character is '^]'.
SENT DO ENCRYPT
SENT WILL ENCRYPT
SENT DO SUPPRESS GO AHEAD
SENT WILL TERMINAL TYPE
SENT WILL NAWS
SENT WILL TSPEED
SENT WILL LFLOW
SENT WILL LINEMODE
SENT WILL NEW-ENVIRON
SENT DO STATUS
RCVD DO NEW-ENVIRON
RCVD WONT ENCRYPT
RCVD DONT ENCRYPT
RCVD WILL SUPPRESS GO AHEAD
RCVD DO TERMINAL TYPE
RCVD DO NAWS
SENT IAC SB NAWS 0 104 (104) 0 51 (51)
RCVD DO TSPEED
RCVD DO LFLOW
RCVD DONT LINEMODE
RCVD WILL STATUS
RCVD IAC SB NEW-ENVIRON SEND
SENT IAC SB NEW-ENVIRON IS
RCVD DO AUTHENTICATION
SENT WONT AUTHENTICATION
RCVD DO XDISPLOC
SENT WONT XDISPLOC
RCVD IAC SB TERMINAL-SPEED SEND
SENT IAC SB TERMINAL-SPEED IS 9600,9600
RCVD IAC SB NEW-ENVIRON SEND
SENT IAC SB NEW-ENVIRON IS
RCVD IAC SB TERMINAL-TYPE SEND
SENT IAC SB TERMINAL-TYPE IS "XTERM-256COLOR"
RCVD DO ECHO
SENT WONT ECHO
RCVD WILL ECHO
SENT DO ECHO
Debian GNU/Linux 12
vit002 login:
```

### Ejercicio 15
¿Quién inicia la negociación de opciones, el cliente o el servidor?
> El cliente

### Ejercicio 16
Ahora haga lo mismo, pero poniendo open máquina 23. ¿Se comporta igual?
```
telnet> open vit002 23
Trying 163.117.171.192...
Connected to vit002.
Escape character is '^]'.
RCVD DO NEW-ENVIRON
SENT WILL NEW-ENVIRON
RCVD IAC SB NEW-ENVIRON SEND
SENT IAC SB NEW-ENVIRON IS
RCVD DO AUTHENTICATION
SENT WONT AUTHENTICATION
RCVD DO TERMINAL TYPE
SENT WILL TERMINAL TYPE
RCVD DO TSPEED
SENT WILL TSPEED
RCVD DO XDISPLOC
SENT WONT XDISPLOC
RCVD IAC SB TERMINAL-SPEED SEND
SENT IAC SB TERMINAL-SPEED IS 9600,9600
RCVD IAC SB NEW-ENVIRON SEND
SENT IAC SB NEW-ENVIRON IS
RCVD IAC SB TERMINAL-TYPE SEND
SENT IAC SB TERMINAL-TYPE IS "XTERM-256COLOR"
RCVD WILL SUPPRESS GO AHEAD
SENT DO SUPPRESS GO AHEAD
RCVD DO ECHO
SENT WONT ECHO
RCVD DO NAWS
SENT WILL NAWS
SENT IAC SB NAWS 0 104 (104) 0 51 (51)
RCVD WILL STATUS
SENT DO STATUS
RCVD DO LFLOW
SENT WILL LFLOW
RCVD WILL ECHO
SENT DO ECHO
Debian GNU/Linux 12
vit002 login:
```
> No, comienza el servidor


### Ejercicio 17
Ahora conéctese al puerto TCP correspondiente a otro servicio, por ejemplo al puerto de control de FTP (el `21`). ¿Intenta el cliente telnet hacer negociación de opciones con el servidor FTP?
```
telnet> open vit002 21
Trying 163.117.171.192...
Connected to vit002.
Escape character is '^]'.
220 (vsFTPd 3.0.3)
```
> No, porque ambos ya conocen las opciones


### Ejercicio 18
Intercambie con el servidor de FTP los comandos que estudiamos en teoría. ¿Qué pasa si intenta listar los ficheros del servidor (`LIST`), o traerse (`RETR`) o llevarse (`STOR`) un fichero?
> Primero te pide que te loggees
```
LIST
530 Please login with USER and PASS.
```
> Después te manda un `500 Unknown command`
```
USER 0429021
331 Please specify the password.
PASS MiSuperContraseña
L230 Login successful.
LIST
500 Unknown command.
```


### Ejercicio 19
Pruebe ahora a hacer el `LIST`, `RETR` y `STOR` con la ayuda del comando `PASV` o `EPSV`. ¿Puede?

```
PASV
227 Entering Passive Mode (163,117,171,192,40,237).
```

> Ahora es necesario abrir otra conexión con telnet al puerto que nos especifican, que es $40*256+237=10477$

```
telnet> open vit002 10477
Trying 163.117.171.192...
Connected to vit002.
Escape character is '^]'.
```

> Al hacer `LIST` en la conexión principal, nos llega la respuesta en la segunda
```
drwxr-xr-x   61 ftp      ftp          4096 Nov 19 04:19 pub
Connection closed by foreign host.
```
