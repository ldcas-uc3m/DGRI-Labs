# Práctica de DNS

El objetivo de esta práctica es experimentar el funcionamiento del protocolo DNS, tanto desde el punto de vista del cliente como del servidor.

Para esto utilizaremos una herramienta para realizar consultas DNS. Proponemos el uso de `dig`, pero se podrían usar otras, como `host` o `nslookup`. De esta forma, podrá comprobar cómo se hacen peticiones de registros de recurso diferentes al servidor por defecto o a uno en concreto, así como interpretar las respuestas.

## Consultas con un cliente DNS (`dig`)
Para realizar esta práctica usaremos el programa dig. A continuación resumimos las principales opciones de este comando, pero le recomendamos que consulte la página de manual completa haciendo man dig . Si lo prefiere, puede usar los programas host o nslookup, de funcionalidad similar a dig.

Las principales opciones de dig son las siguientes:

```
dig [ @server ] [ -p port# ] [ name ] [ type ] [ queryopt... ]
```

- @server: nombre o dirección IP del servidos DNS al que se hace la consulta. Si no se especifica, se consulta a los servidores que se listan en el fichero /etc/resolv.conf.
- -p port: sirve para indicar un puerto de servidor, distinto del de por defecto (53).
- -x: consulta inversa, da DNS y recibe IP
- name: nombre del registro de recurso que se desea consultar.
- type: tipo del registro de recurso que se desea consultar: ANY, A, MX, etc. Si no se sepecifica ninguno, por defecto es el tipo A.
- queryopt: una o más de las siguientes opciones (hay más):
- +tcp: para forzar que la consulta se haga por TCP (por defecto UDP, excepto AXFR o IXFR).
- +notcp para forzar UDP.
- +ignore: para no reintentar consulta por TCP si la respuesta UDP está truncada.
- +norecurse: para forzar consulta iterativa (por defecto es recursiva).

Para ver el DNS usado: `vim /etc/resolv.conf`.

## Ejercicios
Lo primero que vamos a hacer va a ser lanzar un servidor DNS en nuestra propia máquina. Así nos aseguraremos que sólo lo estamos usando nosotros y que su caché sólo va a contener los registros por los que hayamos consultado anteriormente. Se trata de un servidor [bind 9](http://www.isc.org/downloads/bind/).
- Para lanzar el servidor DNS haremos `sudo /etc/init.d/bind9 start`.
- Este servidor DNS no es el servidor por defecto de nuestra máquina, así que en todas las consultas de la práctica indicaremos explícitamente que se hagan las consultas al servidor que hemos lanzado en nuestra máquina (`dig @localhost ...`), salvo que el enunciado nos indique lo contrario.
- Para pararlo haremos `sudo /etc/init.d/bind9 stop`. Cuando lo paramos se borra su cache, así que si lo volvemos a arrancar partirá con una caché vacía. Conviene apagarlo cuando finalicemos la práctica para que no quede el servidor lanzado en nuestra máquina.

### 1. Determine la dirección IP de la máquina `www.mec.es`.
```
$ dig @localhost www.mec.es
...
;; ANSWER SECTION:
www.mec.es.		83898	IN	A	212.128.114.29
...
```

La IP es `212.128.114.29`.

### 2. Averigüe qué máquina tiene asignada la dirección IP 138.100.200.6.

```
$ dig @localhost -x 138.100.200.6
...
;; ANSWER SECTION:
6.200.100.138.in-addr.arpa. 84072 IN	PTR	www.upm.es.
...
```

La URL es `www.upm.es`


### 3. Con relación al dominio xataka.es, averigüe el nombre y dirección IP de los servidores de DNS de dicho dominio, y diga cuál es primario y cuáles secundarios.

Primero obtenemos la IP de `xataka.es`
```
$ dig @localhost xataka.es
...
;; ANSWER SECTION:
xataka.es.		1684	IN	A	103.224.182.248
...
```
El resultado es `103.224.182.248`

Ahora buscamos los servidores DNS mediane una consulta NS:
```
$ dig @localhost xataka.es NS
...
;; ANSWER SECTION:
xataka.es.		84063	IN	NS	ns2.abovedomains.com.
xataka.es.		84063	IN	NS	ns1.abovedomains.com.

;; ADDITIONAL SECTION:
ns1.abovedomains.com.	170637	IN	A	103.224.182.9
ns1.abovedomains.com.	170637	IN	A	103.224.212.9
ns2.abovedomains.com.	170637	IN	A	103.224.182.10
ns2.abovedomains.com.	170637	IN	A	103.224.212.10
...
```
Los servidores son `ns2.abovedomains.com.` y `ns1.abovedomains.com.`. Para consultar ahora cual es el primario realizamos una consulta SOA.

```
$ dig @localhost xataka.es SOA
...
;; ANSWER SECTION:
xataka.es.		60	IN	SOA	ns1.abovedomains.com. hostmaster.trellian.com. 2024102301 10800 3600 604800 3600
...
```
Como vemos, el primario es el `ns1.abovedomains.com.`.


### 4. Obtenga el registro SOA del dominio `xataka.es` preguntándoselo al dns local de la máquina en la que está haciendo la práctica, y preguntándoselo directamente al servidor primario del dominio `xataka.es`. Compruebe que en un caso es información autorizada y en otro no.

```
$ dig @localhost xataka.es SOA
...
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1
...
```

```
$ dig @ns1.abovedomains.com xataka.es
...
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1
...
```

En la segunda consulta está el flag `aa` (authoritative answer) activado, por lo que es una respuesta autoritativa.


### 5. Si tuviera un problema con el DNS de xataka.es y tuviera que mandar un correo electrónico a su administrador, ¿a qué dirección de e-mail lo enviaría?

```
$ dig @localhost xataka.es SOA
...
xataka.es.		60	IN	SOA	ns1.abovedomains.com. hostmaster.trellian.com. 2024102301 10800 3600 604800 3600
...
```

El correo del administrador es `hostmaster@trellian.com`.


### 6. Determine el nombre y dirección IP del servidor de correo del administrador al que se refiere la pregunta anterior.

```
$ dig @localhost trellian.com MX
...
;; ANSWER SECTION:
trellian.com.		86400	IN	MX	10 mx.trellian.com.
...
$ dig @localhost mx.trellian.com
...
;; ANSWER SECTION:
mx.trellian.com.	86400	IN	A	103.224.212.29
mx.trellian.com.	86400	IN	A	103.224.182.44
...
```

El servidor de correo es `mx.trellian.com` y la IP `103.224.212.29`.


### 7. Consulte la dirección IP de `www.lasnoticiasdecuenca.es`. ¿Cuánto tiempo almacenará en cache su DNS local este registro de recurso? Pregunte varias veces a su DNS local por esta dirección. ¿Qué observa en el TTL del registro de recurso?

```
$ dig @localhost www.lasnoticiasdecuenca.es
...
;; ANSWER SECTION:
www.lasnoticiasdecuenca.es. 3262 IN	A	164.138.212.228
...
$ dig @localhost www.lasnoticiasdecuenca.es
...
;; ANSWER SECTION:
www.lasnoticiasdecuenca.es. 3165 IN	A	164.138.212.228
...
```

Obvervamos cómo el TTL disminuye de `3262` a `3165`.


### 8. Pregunte ahora lo mismo a un servidor raíz (p.ej. J.ROOT-SERVERS.NET con dirección IP 192.58.128.30) y compruebe en el paquete de respuesta que, dicho servidor no acepta el modo recursivo.

```
$ dig @J.ROOT-SERVERS.NET www.lasnoticiasdecuenca.es
...
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 45787
;; flags: qr rd; QUERY: 1, ANSWER: 0, AUTHORITY: 4, ADDITIONAL: 9
;; WARNING: recursion requested but not available
...
```
No lleva el flag `ra` (recursive available).


### 9. Averigüe cuantas máquinas están realizando balance de carga en el servidor web `www.elprogreso.es`. ¿Obtiene siempre las mismas y en el mismo orden? Si hace lo mismo con otros servidores que tienen muchas visitas, como www.elpais.es o www.rtve.es, verá que se hace lo mismo pero a través de varias redirecciones a servidores externos (akamai). Akamai es una compañía que proporciona una plataforma de distribución de contenidos (CDN), lo estudiaremos en el tema de web.

```
$ dig @localhost www.elprogreso.es
...
;; ANSWER SECTION:
www.elprogreso.es.	180	IN	A	172.67.70.71
www.elprogreso.es.	180	IN	A	104.26.6.201
www.elprogreso.es.	180	IN	A	104.26.7.201
...
```
Hay tres máquinas para el balanceo de carga, y no siempre en el mismo orden.

```
$ dig @localhost www.elpais.es
...
;; ANSWER SECTION:
www.elpais.es.		3566	IN	CNAME	lb-redireccionesweb-pro-407952733.eu-west-1.elb.amazonaws.com.
lb-redireccionesweb-pro-407952733.eu-west-1.elb.amazonaws.com. 26 IN A 52.16.231.198
lb-redireccionesweb-pro-407952733.eu-west-1.elb.amazonaws.com. 26 IN A 34.248.106.3
...
```


### 10. Haciendo consultas iterativas, averigüe la dirección IP de `www.lahora.com.ec`. ¿Qué pasos ha dado?

```
$ dig @localhost www.lahora.com.ec +norecurse
...
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 49178
;; flags: qr ra ad; QUERY: 1, ANSWER: 0, AUTHORITY: 13, ADDITIONAL: 27
...
;; AUTHORITY SECTION:
.			518067	IN	NS	h.root-servers.net.
.			518067	IN	NS	i.root-servers.net.
.			518067	IN	NS	j.root-servers.net.
.			518067	IN	NS	e.root-servers.net.
.			518067	IN	NS	l.root-servers.net.
.			518067	IN	NS	b.root-servers.net.
.			518067	IN	NS	g.root-servers.net.
.			518067	IN	NS	f.root-servers.net.
.			518067	IN	NS	c.root-servers.net.
.			518067	IN	NS	m.root-servers.net.
.			518067	IN	NS	d.root-servers.net.
.			518067	IN	NS	a.root-servers.net.
.			518067	IN	NS	k.root-servers.net.
...
```
Ha preguntado a todos los root servers y ninguno ha podido contestar.


### 11. Puede hacer esto mismo con la opción +trace de dig. Compruebe el resultado que obtiene.

```
$ dig @localhost www.lahora.com.ec +norecurse +trace
...
; (1 server found)
;; global options: +cmd
.			517919	IN	NS	h.root-servers.net.
.			517919	IN	NS	d.root-servers.net.
.			517919	IN	NS	l.root-servers.net.
.			517919	IN	NS	f.root-servers.net.
.			517919	IN	NS	m.root-servers.net.
.			517919	IN	NS	c.root-servers.net.
.			517919	IN	NS	a.root-servers.net.
.			517919	IN	NS	j.root-servers.net.
.			517919	IN	NS	k.root-servers.net.
.			517919	IN	NS	b.root-servers.net.
.			517919	IN	NS	e.root-servers.net.
.			517919	IN	NS	g.root-servers.net.
.			517919	IN	NS	i.root-servers.net.
.			517919	IN	RRSIG	NS 8 0 518400 20241104050000 20241022040000 61050 . DVAu9jCafNCZNl28T5dowVufjfk8mJzoaLF+gjLDtyHMjL5qvHOiTQCI eH3f/n1ctXQwqsrkukiLa7Dxeb57Q9fElIq5XrzV9aZB+ic0cMO9FeVx pRvlVNZPxiz7Plu1GsqF9/id1Jl3swkUp/2nypHZuAfBD/pBMLJ1Ommp bl+UoxzknHzrn75Z7dAidelVW4n0vjgtb/GVKc3KwUsjRceXxXvRiVux Os1uYwrKnopvy68RSE4U/XbBXbewcMT3VvInYpw5jpgbRNhsiDmyosrx hQvNl6mHSTK8ff3lBPTMpklmdexN0/au3/+HdU9V4XlIDDK82ZUcOYda Q7/JOg==
;; Received 1137 bytes from 127.0.0.1#53(localhost) in 0 ms

ec.			172800	IN	NS	a.lactld.org.
ec.			172800	IN	NS	n2.nic.ec.
ec.			172800	IN	NS	n3.dns.ec.
ec.			172800	IN	NS	ns1.anycastdns.cz.
ec.			172800	IN	NS	ns2.anycastdns.cz.
ec.			86400	IN	DS	35138 13 2 2DE23508E6AC307FC11F86B8768025BFF14E2B585D555B020E218EC6 5C9A0223
ec.			86400	IN	RRSIG	DS 8 1 86400 20241104050000 20241022040000 61050 . RTPGpEMo046W8qyZti0ROTzLmweV+juP3ZM4DPa2ccjaPlaGf+i2swGF zO3EJwNqBbnICwteTZI+56UikzRsUGg6ydExo9Ss7wnP5xuUB5wvgG7t LEpzshSLf7oomSB70bHcdb2tJEJrPJTz/vzluaU5WeysTdIkey/1W6/v DkaO/lF1+dEpCQkhU4rgKphTYyVlnCh4QA8AZ8yl7of2QQkDDW1ilHhJ SMzDcnDthTfd+mirg8X2VQrflLjrw74wu1d1qmCnLOHWaI47KtAjgk+g BHjTZj/M1UPX0bFehuPmKZq/+VOjMRaLOAs5iK1F5OB7GzSi0ZXIu5WE 9ubAwg==
;; Received 690 bytes from 199.7.91.13#53(d.root-servers.net) in 32 ms

lahora.com.ec.		7200	IN	NS	june.ns.cloudflare.com.
lahora.com.ec.		7200	IN	NS	leonard.ns.cloudflare.com.
eh3551foalbc7dnaiq5qnnjbnbaucos6.com.ec. 300 IN	NSEC3 1 0 3 A50E28AF9D2A8745 EH4MPLI5IM1SCIBEDCKVK1AL3QEUE97L NS
eh3551foalbc7dnaiq5qnnjbnbaucos6.com.ec. 300 IN	RRSIG NSEC3 13 3 300 20241106092629 20241022083835 38558 com.ec. +/Ba1WMqY3j/HQCzUpNFMZGLlkIuCC0ShQMvJGFIVZfPT95csWHlLwAE GU8/f5nEKOYtLDiVosypNUqNPDdCEw==
eh3551foalbc7dnaiq5qnnjbnbaucos6.com.ec. 300 IN	RRSIG NSEC3 13 3 300 20241106092629 20241022083835 44928 com.ec. ey8w3qxqqcDAiV2D152gGdoHguG2wf3arB11GwCkqPgqseyiHXJFq/yM +itz5CbDuxLwmssx3q7MeO8J9yCY2A==
;; Received 390 bytes from 185.38.108.108#53(ns1.anycastdns.cz) in 128 ms

www.lahora.com.ec.	300	IN	A	104.26.15.143
www.lahora.com.ec.	300	IN	A	104.26.14.143
www.lahora.com.ec.	300	IN	A	172.67.75.106
...
```

Ahora sí que lo ha encontrado, ya que el cliente ha consultado directamente a todos los servidores.


### 12. Utilizando la información disponible a través del DNS determine (nombre y dirección IP) la máquina o máquinas que actúan como servidoras de correo del dominio `gmail.com`.

```
$ dig @localhost gmail.com MX
...
;; ANSWER SECTION:
gmail.com.		3598	IN	MX	30 alt3.gmail-smtp-in.l.google.com.
gmail.com.		3598	IN	MX	20 alt2.gmail-smtp-in.l.google.com.
gmail.com.		3598	IN	MX	10 alt1.gmail-smtp-in.l.google.com.
gmail.com.		3598	IN	MX	40 alt4.gmail-smtp-in.l.google.com.
gmail.com.		3598	IN	MX	5 gmail-smtp-in.l.google.com.
...
$ dig @localhost gmail.com alt3.gmail-smtp-in.l.google.com alt2.gmail-smtp-in.l.google.com alt1.gmail-smtp-in.l.google.com alt4.gmail-smtp-in.l.google.com gmail-smtp-in.l.google.com
...
gmail.com.		300	IN	A	142.250.200.101
...
alt3.gmail-smtp-in.l.google.com. 296 IN	A	142.251.1.27
...
alt2.gmail-smtp-in.l.google.com. 296 IN	A	142.251.9.27
...
alt1.gmail-smtp-in.l.google.com. 296 IN	A	142.250.153.26
...
alt4.gmail-smtp-in.l.google.com. 296 IN	A	74.125.200.27
...
gmail-smtp-in.l.google.com. 296	IN	A	74.125.133.27
...
```


### 13. ¿Cómo haría para obtener todos los registros de recurso de la zona `lab.it.uc3m.es`?

```
$ dig @localhost lab.it.uc3m.es IXFR
...
lab.it.uc3m.es.		34	IN	A	163.117.144.200
lab.it.uc3m.es.		34	IN	A	163.117.144.129
...
```
Nota: aquí se debe usar `AXFR`, pero aquí está capado.


