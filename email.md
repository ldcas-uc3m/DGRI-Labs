# Práctica de correo electrónico
En esta práctica vamos a probar los protocolos relacionados con el correo electrónico. A lo largo de esta práctica, vamos a suponer que su login es `0xxxxxx`.

## Ejercicios

### Ejercicio 1
> Todos los puestos del laboratorio tienen un servidor de correo lanzado. Averigüe, haciendo un `telnet` a su propia máquina y al puerto que corresponda, si el servidor de correo soporta o no ESMTP. En caso afirmativo, diga qué opciones de ESMTP soporta. Consulte las RFCs [1854](http://tools.ietf.org/html/rfc1854) y [1870](http://tools.ietf.org/html/rfc1870) para entender en qué consisten las opciones `PIPELINING` y `SIZE`.

```
$ telnet localhost SMTP
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
220 vit125.lab.it.uc3m.es ESMTP Postfix
EHLO vit125.lab.it.uc3m.es
250-vit125.lab.it.uc3m.es
250-PIPELINING
250-SIZE 10240000
250-VRFY
250-ETRN
250-STARTTLS
250-ENHANCEDSTATUSCODES
250-8BITMIME
250-DSN
250 CHUNKING
```

Al conectarnos nos muestra que soporta ESMTP. Usamos `EHLO <hostname>` para comprobar qué opciones soporta.


### Ejercicio 2

> Haciendo `telnet` al puerto SMTP de esa máquina, envíe un correo a su cuenta del laboratorio de Telemática (0xxxxxx@lab.it.uc3m.es) y compruebe que lo puede leer desde un terminal de su ordenador con el comando `mail` de Unix. Diga una dirección origen falsa en el protocolo SMTP. Ponga en el cuerpo del mensaje las cabeceras [RFC822](https://datatracker.ietf.org/doc/html/rfc822) más importantes, como `From`, `Subject`, `To`, `Cc` y `Date`. Ponga otras direcciones falsas distintas en las cabeceras `From`, `To` y `Cc` del mensaje.

> [!NOTE]
> Para leer un mensaje dentro del entorno de `mail`, simplemente hay que escribir el número del mensaje junto al prompt (`&`) y pulsar el retorno de carro. Para salir, lo que se introduce es una `x`.

```
$ telnet localhost SMTP
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
220 vit125.lab.it.uc3m.es ESMTP Postfix
EHLO lab.it.uc3m.es
250-vit125.lab.it.uc3m.es
250-PIPELINING
250-SIZE 10240000
250-VRFY
250-ETRN
250-STARTTLS
250-ENHANCEDSTATUSCODES
250-8BITMIME
250-DSN
250 CHUNKING
MAIL FROM: feik@lab.it.uc3m.es
250 2.1.0 Ok
RCPT TO: 0429021@lab.it.uc3m.es
250 2.1.5 Ok
DATA
354 End data with <CR><LF>.<CR><LF>
From: feik@lab.it.uc3m.es
To: 0429021@lab.it.uc3m.es
Cc: admin@lab.it.uc3m.es
Date: Sat Nov 23 19:45:03 2024
Subject: Test mail

We've been trying to reach you about your car's extended warranty.
.
250 2.0.0 Ok: queued as B88CC280336
QUIT
221 2.0.0 Bye
Connection closed by foreign host.
```

```
$ mail
Mail version 8.1.2 01/15/2001.  Type ? for help.
"/var/mail/0429021": 1 message 1 new
>N  1 feik@lab.it.uc3m.  Sat Nov 23 19:48   29/1375  Test mail
& 1
Message 1:
From feik@lab.it.uc3m.es  Sat Nov 23 19:48:38 2024
X-Original-To: 0429021@lab.it.uc3m.es
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed; d=lab.it.uc3m.es;
        s=mail; t=1732387718;
        bh=rLSo3v+b1bqsJYKmkm4Zl3PsJTwW6qzTgaQZFouOqUs=;
        h=From:To:Cc:Date:Subject:From;
        b=bZC4VDQ+jTUYByXnJIchJnlMEYAntx5IkPM9l46HfNNzZm1KRwT5qZl9kUNT6BF1J
         TIcc2HVg0WBdrAH57hI+C6o2b/vW5Gc1y6VeKsh6T8019wa4fiv+bzoZjvCYpLYEmi
         UIOe/jyM0IQcPFRD7q0TOvFP8TW5KY1xgBWTQpzI=
From: feik@lab.it.uc3m.es
To: 0429021@lab.it.uc3m.es
Cc: admin@lab.it.uc3m.es
Date: Sat Nov 23 19:45:03 2024
Subject: Test mail

We've been trying to reach you about your car's extended warranty.
```


### Ejercicio 3
> Mándese más correos a la cuenta del laboratorio de Telemática, ahora desde el interfaz web de la universidad o desde otra cuenta que tenga abierta en otro proveedor (gmail, hotmail, etc). Ponga en varios de ellos la palabra `prueba` en el cuerpo del mensaje, en otros en el asunto del mensaje, y en otros que no aparezca esta palabra. Haga que alguno de los correos tenga un fichero adjunto (una imagen GIF) y texto con acentos.



### Ejercicio 4
> Compruebe que ha recibido los mensajes (con el comando `mail` de Unix). ¿Cómo ve con mail el mensaje que tenía un fichero asociado? ¿por qué?

```
$ mail
Mail version 8.1.2 01/15/2001.  Type ? for help.
"/var/mail/0429021": 4 messages 4 new
>N  1 feik@lab.it.uc3m.  Sat Nov 23 19:48   29/1375  Test mail
 N  2 100429021@alumnos  Sat Nov 23 19:54   89/4915
 N  3 100429021@alumnos  Sat Nov 23 19:55   92/5130  prueba
 N  4 100429021@alumnos  Sat Nov 23 19:56 6495/497588 noot noot
& 4
From: LUIS DANIEL CASAIS MEZQUIDA <100429021@alumnos.uc3m.es>
Date: Sat, 23 Nov 2024 19:55:10 +0100
Subject: noot noot
To: 0429021@lab.it.uc3m.es
Content-Type: multipart/mixed; boundary="0000000000006dd7bc0627990dc1"
X-TM-AS-GCONF: 00
X-TM-AS-Product-Ver: IMSVA-9.1.0.1689-9.0.0.1006-28816.001
X-TM-AS-Result: No--3.949-5.0-31-10
X-imss-scan-details: No--3.949-5.0-31-10
X-TMASE-Version: IMSVA-9.1.0.1689-9.0.1006-28816.001
X-TMASE-Result: 10--3.949400-10.000000
X-TMASE-MatchedRID: LkeYFEC97tfdKephRYrhGinjDVPUYtjj7euC4Hkicftto7jS2jaQmcYW
        bVREZVDEOszJ/mD5RnyyjMAd3qWSjf+z7xWvCPyPL4+sB3yBscmBztcinfnHJ9a5uIySSCA++Bu
        o4Q3kVMolyItZ36xSGWRinB1Mnjd9lR1cT9YafQWY/fxzcM2LJNFTca7CU2+Xs2IXVKFKcibmVu
        Tu1vVZhBac8mCxN1yrZYIs926SF9q1yritD5b6J2uIyeoH4CMtJpLY3HDIakoVQlpmlaPvNk/Rq
        RHQEEIwaQ43mFiw/0F8rHemNtGCi1A6jbpidjgc4HnNasxswwbwxgalMhapDfDV0r0+ZABu7w5J
        DMW2E4M=
X-TMASE-SNAP-Result: 1.821001.0001-0-1-22:0,33:0,34:0-0

--0000000000006dd7bc0627990dc1
Content-Type: multipart/alternative; boundary="0000000000006dd7b90627990dbf"

--0000000000006dd7b90627990dbf
Content-Type: text/plain; charset="UTF-8"



--0000000000006dd7b90627990dbf
Content-Type: text/html; charset="UTF-8"

<div dir="ltr"><div class="gmail_default" style="font-family:georgia,serif"><br></div></div>

--0000000000006dd7b90627990dbf--
--0000000000006dd7bc0627990dc1
Content-Type: image/gif; name="noot-noot.gif"
Content-Disposition: attachment; filename="noot-noot.gif"
Content-Transfer-Encoding: base64
Content-ID: <f_m3uj5f1y0>
X-Attachment-Id: f_m3uj5f1y0

R0lGODlh3ACRAPcAAAABAAADAAECAAIDAQIFAgIFAwIHBAYJBgkNCgoRDgsSDwwTEQ0VExEXFRYc
...
BlfgO+sFui4YXotwlLOd/x/5ohwQADs=
--0000000000006dd7bc0627990dc1--

```

El mail con el fichero asociado, al ser tan grande, se ve usando la herramienta `less` de Unix, y el fichero viene codificado como una imagen en base64.


### Ejercicio 5
> Sus correos se almacenan en su buzón de entrada, que está en el fichero `/var/spool/mail/0xxxxxx`. Abra este fichero con un editor de texto y observe los mensajes de correo. En particular, observe y anote las cabeceras MIME del mensaje que ha enviado con la imagen GIF adjunta. ¿Cuántas partes lo componen? ¿cómo está codificada cada una de ellas?

Viene en un _multipart_ con un apartado de texto plano y otro HTML para darle el formato del provedor (Gmail), y una imagen GIF en base64.



### Ejercicio 6
> Conéctese con telnet al puerto POP3 de su máquina.
```
$ telnet localhost 110
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
+OK Dovecot (Debian) ready.
```
> Ejecute los comandos necesarios para hacer lo siguiente y anote las respuestas:
> 1. Autentíquese
```
USER 0429021
+OK
PASS MiSuperContraseña
+OK Logged in.
```
> 2. Averigüe cuántos correos tiene en su buzón y cuántos bytes ocupan en total
```
STAT
+OK 4 515479
```
Tengo 4 mensajes, ocupando 515479B (~500KB).
> 3. Avegigüe cuántos bytes ocupa el último mensaje
```
LIST
+OK 4 messages:
1 1350
2 4944
3 5162
4 504023
```
El útlimo mensaje ocupa 504023B
> 4. Descárguese sólo las cabeceras del último mensaje
```
TOP 4 0
+OK
Return-Path: <100429021@alumnos.uc3m.es>
...
```
> 5. Descárguese el último mensaje completo (cabeceras y cuerpo)
```
RETR 4
+OK
...
```
> 6. Marque el último mensaje como borrado (**no cierre la conexión**)
```
DELE 4
+OK Marked to be deleted.
```



### Ejercicio 7
> Haga ahora un "reset" y finalice la conexión POP3. Compruebe si se ha borrado o no el mensaje último. Para ello vuelva a conectarse y compruebe si el mensaje sigue allí.
```
RSET
+OK
STAT
+OK 4 515479
```
El mensaje no se ha borrado, ya que hemos reseteado la conexión.



### Ejercicio 8
> Conéctese ahora con `telnet` al puerto IMAP de su máquina. Averigüe qué funciones opcionales de IMAP soporta el servidor de su máquina. ¿Qué comando ha usado para averiguarlo?
```
$ telnet localhost 143
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
* OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE LITERAL+ AUTH=PLAIN] Dovecot (Debian) ready.
A001 CAPABILITY
* CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE LITERAL+ AUTH=PLAIN
A001 OK Pre-login capabilities listed, post-login capabilities have more.
```


### Ejercicio 9
> Ejecute los comandos IMAP necesarios para hacer lo siguiente y anote las respuestas:
> 1. Autentíquese
```
A001 LOGIN 0429021 MiSuperContraseña
A001 OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE SORT SORT=DISPLAY THREAD=REFERENCES THREAD=REFS THREAD=ORDEREDSUBJECT MULTIAPPEND URL-PARTIAL CATENATE UNSELECT CHILDREN NAMESPACE UIDPLUS LIST-EXTENDED I18NLEVEL=1 CONDSTORE QRESYNC ESEARCH ESORT SEARCHRES WITHIN CONTEXT=SEARCH LIST-STATUS BINARY MOVE SNIPPET=FUZZY PREVIEW=FUZZY PREVIEW STATUS=SIZE SAVEDATE LITERAL+ NOTIFY SPECIAL-USE] Logged in
```
> 2. Seleccione el buzón de entrada
```
A002 SELECT INBOX
* FLAGS (\Answered \Flagged \Deleted \Seen \Draft)
* OK [PERMANENTFLAGS (\Answered \Flagged \Deleted \Seen \Draft \*)] Flags permitted.
* 4 EXISTS
* 0 RECENT
* OK [UNSEEN 1] First unseen.
* OK [UIDVALIDITY 1732389038] UIDs valid
* OK [UIDNEXT 5] Predicted next UID
A002 OK [READ-WRITE] Select completed (0.035 + 0.000 + 0.034 secs).
```
> 3. Averigüe cuántos correos tiene en su buzón
```
A003 STATUS INBOX (MESSAGES)
* STATUS INBOX (MESSAGES 4)
A003 OK [CLIENTBUG] Status on selected mailbox completed (0.001 + 0.000 secs).
```
> 4. Avegigüe cuántos bytes ocupa el último mensaje
```
A004 FETCH 4 (RFC822.SIZE)
* 4 FETCH (RFC822.SIZE 504023)
A004 OK Fetch completed (0.012 + 0.000 + 0.011 secs).
```
> 5. Descárguese sólo la estructura del último mensaje
```
A005 FETCH 4 (BODYSTRUCTURE)
* 4 FETCH (BODYSTRUCTURE ((("text" "plain" ("charset" "UTF-8") NIL NIL "7bit" 2 1 NIL NIL NIL NIL)("text" "html" ("charset" "UTF-8") NIL NIL "7bit" 94 1 NIL NIL NIL NIL) "alternative" ("boundary" "0000000000006dd7b90627990dbf") NIL NIL NIL)("image" "gif" ("name" "noot-noot.gif") "<f_m3uj5f1y0>" NIL "base64" 498686 NIL ("attachment" ("filename" "noot-noot.gif")) NIL NIL) "mixed" ("boundary" "0000000000006dd7bc0627990dc1") NIL NIL NIL))
A005 OK Fetch completed (0.029 + 0.000 + 0.028 secs).
```
> 6. Descárguese el último mensaje completo (cabeceas y cuerpo)
```
A006 FETCH 4 (BODY[HEADER] BODY[TEXT])
* 4 FETCH (BODY[HEADER] {4670}
...
 BODY[TEXT] {499353}
...
A006 OK Fetch completed (0.020 + 0.000 + 0.019 secs).
```
> 7. Marque el último mensaje como borrado
```
A007 STORE 4 +FLAGS \Deleted
* 4 FETCH  (FLAGS (\Seen \Deleted))
a007 OK +FLAGS completed
```


### Ejercicio 10
> Averigüe cuál es el delimitador de jerarquía de este servidor IMAP. Envíe los comandos IMAP necesarios para crear un buzón llamado `prueba` dentro de una carpeta `laboratorio`. Observe en otra ventana, en un terminal, en qué lugar de su cuenta se han creado estos ficheros.
```
A008 LIST "" *
* LIST (\HasNoChildren) "/" INBOX
A008 OK List completed (0.012 + 0.000 + 0.011 secs).
A009 CREATE laboratorio/prueba
A009 OK Create completed (0.073 + 0.000 + 0.072 secs).
```

```
$ ls -lah ~
...
drwxrwx---   4 0429021 0429021   48 Nov 23 20:49 mail/
...
$ tree mail/
mail
└── laboratorio
    └── prueba
```


### Ejercicio 11
> Busque en el buzón de entrada por defecto los correos que cumplen el criterio de búsqueda de que aparece la palabra `prueba` en el asunto del mensaje.
```
A010 search subject prueba
* SEARCH 3
A010 OK Search completed (0.028 + 0.000 + 0.027 secs).
```


### Ejercicio 12
> Copie los correos que cumplan el criterio de búsqueda anterior al buzón prueba que creo en el apartado 12. Seleccione este buzón y compruebe que los mensajes están allí.
```
A011 COPY 3 laboratorio/prueba
A011 OK [COPYUID 1732391390 3 1] Copy completed (0.060 + 0.000 + 0.059 secs).
A012 SELECT laboratorio/prueba
* OK [CLOSED] Previous mailbox closed.
* FLAGS (\Answered \Flagged \Deleted \Seen \Draft)
* OK [PERMANENTFLAGS (\Answered \Flagged \Deleted \Seen \Draft \*)] Flags permitted.
* 1 EXISTS
* 1 RECENT
* OK [UIDVALIDITY 1732391390] UIDs valid
* OK [UIDNEXT 2] Predicted next UID
A012 OK [READ-WRITE] Select completed (0.023 + 0.000 + 0.022 secs).
```

### Ejercicio 13
> Vamos ahora a probar algunas medidas que se han tomado contra el _spam_ que se han comentado en clase. Empezaremos por comprobar las políticas SPF de un dominio. Imagine que un MTA recibe un correo de una `direccion@lab.it.uc3m.es`. Diga que consulta haría para comprobar las políticas SPF de este dominio. Realice la consulta y diga qué máquinas están autorizadas a enviar correos que tengan como remitente `direccion@lab.it.uc3m.es`.
```
$ dig -t TXT lab.it.uc3m.es

; <<>> DiG 9.18.16-1~deb12u1-Debian <<>> -t TXT lab.it.uc3m.es
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 17009
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 8e719d3368fa836a01000000674234726534244c9ec89ac7 (good)
;; QUESTION SECTION:
;lab.it.uc3m.es.                        IN      TXT

;; ANSWER SECTION:
lab.it.uc3m.es.         60      IN      TXT     "MS=ms84224798"
lab.it.uc3m.es.         60      IN      TXT     "v=spf1 include:_spf.uc3m.es ~all"

;; Query time: 0 msec
;; SERVER: 163.117.144.129#53(163.117.144.129) (UDP)
;; WHEN: Sat Nov 23 21:00:50 CET 2024
;; MSG SIZE  rcvd: 142
```

```
$ dig -t TXT _spf.uc3m.es

; <<>> DiG 9.18.16-1~deb12u1-Debian <<>> -t TXT _spf.uc3m.es
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 31411
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: ab9c5d753f9f2fae0100000067423528d07e194b2bdca62b (good)
;; QUESTION SECTION:
;_spf.uc3m.es.                  IN      TXT

;; ANSWER SECTION:
_spf.uc3m.es.           300     IN      TXT     "v=spf1 ip4:163.117.0.0/16 ip4:176.58.10.138 ip6:2001:720:410::/48  ip4:194.140.59.15 include:_spf.google.com include:_spf.salesforce.com -all"

;; Query time: 28 msec
;; SERVER: 163.117.144.129#53(163.117.144.129) (UDP)
;; WHEN: Sat Nov 23 21:03:52 CET 2024
;; MSG SIZE  rcvd: 223
```

Están autorizadas las máquinas que estén autorizadas por el SPF de `_spf.uc3m.es`, que son las direcciones IPv4 `163.117.0.0/16` y `176.58.10.138`, y las IPv6 `2001:720:410::/48`, mas las proporcionadas por `_spf.google.com` y `_spf.salesforce.com`.


### Ejercicio 14
> Vamos a observar ahora una cabecera DKIM. Envíese un correo desde una cuenta de Gmail. Observe la cabecera DKIM-Signature que recibe. ¿Qué campos de la cabecera del correo están firmados en DKIM-Signature? ¿Qué algoritmos de resumen de mensaje y de firma se han usado? Lea el [apartado 3.6.2 de la RFC 6376](https://datatracker.ietf.org/doc/html/rfc6376#section-3.6.2) y averigüe cómo puede obtener la clave pública del servidor con la que verificar esta firma.

La cabezera DKIM es:
```
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed;
        d=alumnos.uc3m.es; s=google; t=1732388101; x=1732992901; darn=lab.it.uc3m.es;
        h=to:subject:message-id:date:from:mime-version:from:to:cc:subject
         :date:message-id:reply-to;
        bh=0/2h3LjDputYqOqKE/27nd8OhcK6Ch+AnorM40arFis=;
        b=FsWDuUYhUFpyjFlbxf1f+KQPFeBczjbzSgH8LGb7A9TSwjkvc6IOEEIo+UKjWaB0Sm
         fcrHQQ8oceKOyY4jFqdtunGeRVL041zAEJQIHU0isEmJaRhZzippKTnCYAqJ78uQPVvo
         9MzZU4vgqs/2/S2klhPVYFTgo2dU905vSZIWns4HFRdDpUslLOcFgL2TWLvZrwmF+53M
         bBVXTlk7esPMLJwQgJz1dxggN6530nH+tI8axxVu9Hps9CoSfbPiFvz3Hen/EXj8Yr3/
         bKa9qSTf6SKOaAmAWDdzPkrchTKa6fDWgTcG5us7KziMbCiqsA/55d1DANIlEemm4C9P
         vgMA==
X-Google-DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed;
        d=1e100.net; s=20230601; t=1732388101; x=1732992901;
        h=to:subject:message-id:date:from:mime-version:x-gm-message-state
         :from:to:cc:subject:date:message-id:reply-to;
        bh=0/2h3LjDputYqOqKE/27nd8OhcK6Ch+AnorM40arFis=;
        b=UcwC02UVGuD6SbJMaCJkLyyWnqpevthEceyKoJ5tLzChuBxlWzKZ8eam3fGtWoq9ff
         6hrTmdzumIS1xn/0CZe2/njkAhpN6IVCjAr1plVa6PCqa/CPNerUQm8Q66A9YJ7/nTe1
         Pkba8tvukJlgHoCPpDRehwgZubApYoiRceKkxTWrn+wAVKLk1P7QegvXEXOmg1AvnG5+
         ahur9tkhGHNR+v8UPdw7o3bDdk9eAxP2PXASgzqFmRvkXq1A8z0PWNrWlqR9vjB0no7I
         6MyVTffi2OtC0Z5md7YYK0TMwzoRvNpaxiyEsIr/7uxQEmbZW58v7NP+I69L6iv5YJr/
         Ry4A==
```
Protege todos los campos, y usa cifrado RSA-SHA-256.

Para obtener la clave pública para verificar la firma, basta con realizar una consulta DNS de tipo TXT a `google._domainkey.alumnos.uc3m.es`:
```
$ dig -t TXT google._domainkey.alumnos.uc3m.es

; <<>> DiG 9.18.16-1~deb12u1-Debian <<>> -t TXT google._domainkey.alumnos.uc3m.es
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 41906
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 51feef3df650094901000000674237c4a9eb2d66283d9426 (good)
;; QUESTION SECTION:
;google._domainkey.alumnos.uc3m.es. IN  TXT

;; ANSWER SECTION:
google._domainkey.alumnos.uc3m.es. 59303 IN TXT "v=DKIM1; k=rsa; p=MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEApAgIsSm5O97Kjpyuq8EgseOXqayuuns/LD1SQsneI/zZK0fhrgloVfI1TBVF0pryXKa5I8+qf9JS1ZKNTqglSsJD4vpSCUhwweQW42j9O3W3kN5GIULeBTfLJPUxWxq697FIeSCSFLG/aH6vQ47P6/FSto" "J2E0L2pZmfi/jB8UOwBATW2u0cwjaOXyg1vSTqUyFQSOhV9MrlehFQzwOwNTf0SvLd3ymjS3fdslomaxxnZtlkIqYkr5sFjU9MTRLTy2QnZXDqkNEheRvzf5aPrGaqOeiDi4THzHTI8M31aWBR/qRHt9muC+Hrdm/PRNPU0vyiDS54GvkF8n7euEJHZQIDAQAB"

;; Query time: 4 msec
;; SERVER: 163.117.144.129#53(163.117.144.129) (UDP)
;; WHEN: Sat Nov 23 21:15:00 CET 2024
;; MSG SIZE  rcvd: 514
```


### Ejercicio 15
> Los protocolos de correo electrónico (SMTP, POP3, IMAP) disponen de puertos reservados para usarse exclusivamente con TLS (465, 995 y 993 respectivamente). Podemos conectarnos a estos puertos e iniciar una sesión TLS usando el comando `openssl`, de forma similar a como hacemos con `telnet` para los puertos TCP en claro.  
> Pruébelos con los servidores seguros de algún proveedor de correo electrónico, por ejemplo:
> ```
> openssl s_client -connect smtp.gmail.com:465
> ```
```
CONNECTED(00000003)
depth=2 C = US, O = Google Trust Services LLC, CN = GTS Root R1
verify return:1
depth=1 C = US, O = Google Trust Services, CN = WR2
verify return:1
depth=0 CN = smtp.gmail.com
verify return:1
---
Certificate chain
 0 s:CN = smtp.gmail.com
   i:C = US, O = Google Trust Services, CN = WR2
   a:PKEY: id-ecPublicKey, 256 (bit); sigalg: RSA-SHA256
   v:NotBefore: Oct 21 08:37:56 2024 GMT; NotAfter: Jan 13 08:37:55 2025 GMT
 1 s:C = US, O = Google Trust Services, CN = WR2
   i:C = US, O = Google Trust Services LLC, CN = GTS Root R1
   a:PKEY: rsaEncryption, 2048 (bit); sigalg: RSA-SHA256
   v:NotBefore: Dec 13 09:00:00 2023 GMT; NotAfter: Feb 20 14:00:00 2029 GMT
 2 s:C = US, O = Google Trust Services LLC, CN = GTS Root R1
   i:C = BE, O = GlobalSign nv-sa, OU = Root CA, CN = GlobalSign Root CA
   a:PKEY: rsaEncryption, 4096 (bit); sigalg: RSA-SHA256
   v:NotBefore: Jun 19 00:00:42 2020 GMT; NotAfter: Jan 28 00:00:42 2028 GMT
---
Server certificate
-----BEGIN CERTIFICATE-----
MIIEVjCCAz6gAwIBAgIQCJe5iM0zkpwKOEDHPpBryDANBgkqhkiG9w0BAQsFADA7
MQswCQYDVQQGEwJVUzEeMBwGA1UEChMVR29vZ2xlIFRydXN0IFNlcnZpY2VzMQww
CgYDVQQDEwNXUjIwHhcNMjQxMDIxMDgzNzU2WhcNMjUwMTEzMDgzNzU1WjAZMRcw
FQYDVQQDEw5zbXRwLmdtYWlsLmNvbTBZMBMGByqGSM49AgEGCCqGSM49AwEHA0IA
BCvbjDF9Zm53bf8ZIIe4p+mVCA0Sl/GlC5EiKHLKkRqhUqCtKpoqyOokmPynRja0
feO+Rq7s0OgUFEvwg0rJ2cqjggJBMIICPTAOBgNVHQ8BAf8EBAMCB4AwEwYDVR0l
BAwwCgYIKwYBBQUHAwEwDAYDVR0TAQH/BAIwADAdBgNVHQ4EFgQU9kmK1QMBpEDS
zi3v0zUiIMc71IswHwYDVR0jBBgwFoAU3hse7XkV1D43JMMhu+w0OW1CsjAwWAYI
KwYBBQUHAQEETDBKMCEGCCsGAQUFBzABhhVodHRwOi8vby5wa2kuZ29vZy93cjIw
JQYIKwYBBQUHMAKGGWh0dHA6Ly9pLnBraS5nb29nL3dyMi5jcnQwGQYDVR0RBBIw
EIIOc210cC5nbWFpbC5jb20wEwYDVR0gBAwwCjAIBgZngQwBAgEwNgYDVR0fBC8w
LTAroCmgJ4YlaHR0cDovL2MucGtpLmdvb2cvd3IyL29RNm55cjhGMG0wLmNybDCC
AQQGCisGAQQB1nkCBAIEgfUEgfIA8AB2AE51oydcmhDDOFts1N8/Uusd8OCOG41p
wLH6ZLFimjnfAAABkq5wwEAAAAQDAEcwRQIgSaYuP08bgYYoHJucOGgIkg6PJO9Q
6hiogiaDXPVT8AICIQDkqqjL5LULhc9Y8GNIbTAp1DGDqZ86FWYixcMeYfVj5QB2
AKLjCuRF772tm3447Udnd1PXgluElNcrXhssxLlQpEfnAAABkq5wwFAAAAQDAEcw
RQIhALrropMkeLEkB9BmIwtmTyXrJxtTtuuZDDoh70MqTXwnAiAXfXvr9NQSu3+D
iDnIMWPYfUk4pumHvWNXPwOPEkgJOTANBgkqhkiG9w0BAQsFAAOCAQEAYJVbJIjN
m8JcuswN5Wc0z9wgj2cCAMJJLCEbiup2yzk3uvl8m3otpGTU553GasniZba72X1G
G/LqhVnt3CqULoDWyUHCK3YG28xtNDebH3w1Y7P1JmNTS7UQRSsWtW56rH528Shr
07tVYAfQ/rkcX62ofMAo0yHOpLRpFLfr7uStJVZonEDKGo8P3HwnSbfP9fOVUsL6
3XwYVWSAWu56YPYYOLKXEDqKMW5HsrdBrV/+vXMKosQDIKTy/8X9HRLE/zjxsqnA
Yf9ZbqnD/Yhb/Vzq+FmT41T7ym1PTcJRs9ZgLywvHNjRb2iiwyJoDPSEhUBE4/E0
FFAroJfW8Ylh6g==
-----END CERTIFICATE-----
subject=CN = smtp.gmail.com
issuer=C = US, O = Google Trust Services, CN = WR2
---
No client certificate CA names sent
Peer signing digest: SHA256
Peer signature type: ECDSA
Server Temp Key: X25519, 253 bits
---
SSL handshake has read 4106 bytes and written 400 bytes
Verification: OK
---
New, TLSv1.3, Cipher is TLS_AES_256_GCM_SHA384
Server public key is 256 bit
Secure Renegotiation IS NOT supported
Compression: NONE
Expansion: NONE
No ALPN negotiated
Early data was not sent
Verify return code: 0 (ok)
---
---
Post-Handshake New Session Ticket arrived:
SSL-Session:
    Protocol  : TLSv1.3
    Cipher    : TLS_AES_256_GCM_SHA384
    Session-ID: 4354E9E9F9F398818E63A57651D8C3DE6C5B916C9868E16DA2D946E55ACCD28A
    Session-ID-ctx:
    Resumption PSK: 070DD0020DCE12C683EF1E54F18AE9F0E97826DE3A81B74B51EAFE4B0D862EA449073EADC68A0736197D74379A43B50B
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 172800 (seconds)
    TLS session ticket:
    0000 - 02 8c 74 fd e8 08 e3 9a-96 43 10 56 39 64 65 9c   ..t......C.V9de.
    0010 - f7 ce cb e7 95 89 83 89-d8 3f b4 a1 db fa b3 c7   .........?......
    0020 - 2c 46 57 ba 1f 94 42 ff-00 6d 24 3f b6 f4 d9 39   ,FW...B..m$?...9
    0030 - 37 16 d8 f9 f2 8e 02 d4-73 eb b1 23 e8 75 8a 81   7.......s..#.u..
    0040 - 06 92 a6 ab 97 93 cb 75-70 03 db 08 99 00 f5 81   .......up.......
    0050 - c0 ea 2f 88 44 b7 20 bf-a7 f7 f8 10 23 28 9b 39   ../.D. .....#(.9
    0060 - 95 20 c4 80 33 94 95 23-df ea 37 ee 07 99 1c dd   . ..3..#..7.....
    0070 - ee 56 5c 03 20 8f 86 18-75 3e 90 2d 7a a0 c8 dc   .V\. ...u>.-z...
    0080 - cf 37 bc 34 69 d0 56 59-7a 0c 6e cd 05 a1 8f a5   .7.4i.VYz.n.....
    0090 - f1 14 c2 32 e0 fd c2 82-9e 97 37 f7 a2 7e 83 e7   ...2......7..~..
    00a0 - bb 30 62 7a 7d 4c ad 35-a3 a3 d9 4e 7f 1f bb 5e   .0bz}L.5...N...^
    00b0 - a0 2d d0 af d2 2b e7 cd-29 07 ac c8 20 2a c3 c6   .-...+..)... *..
    00c0 - 8f 62 0d 57 56 ef fa 26-2c e5 1b 6c ca 99 6a 19   .b.WV..&,..l..j.
    00d0 - 9f 77 db 52 ca cc 43 ab-c4 72 d4 0b 1d f8 7b fa   .w.R..C..r....{.
    00e0 - be 3b 03 6c 42 52 55 50-91 60 1a 32 25 d9 17      .;.lBRUP.`.2%..

    Start Time: 1732404453
    Timeout   : 7200 (sec)
    Verify return code: 0 (ok)
    Extended master secret: no
    Max Early Data: 0
---
read R BLOCK
---
Post-Handshake New Session Ticket arrived:
SSL-Session:
    Protocol  : TLSv1.3
    Cipher    : TLS_AES_256_GCM_SHA384
    Session-ID: 21ACD358C8FD13785623BB3EEB37B6827AA06C1A3B9CC187C82AB781F3226113
    Session-ID-ctx:
    Resumption PSK: EFB291A9213830D09CBE9E8E952404427B90EB102F80A589383A15EC9488039648FD35356714C63C3F80FB968ED6206A
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 172800 (seconds)
    TLS session ticket:
    0000 - 02 8c 74 fd e8 08 e3 9a-96 43 10 56 39 64 65 9c   ..t......C.V9de.
    0010 - 53 3b 3c 2f 21 a8 74 39-f0 63 c0 40 e7 af 37 21   S;</!.t9.c.@..7!
    0020 - 5d ee 75 da 9a af 4f 7a-37 a5 98 a8 82 39 8c 28   ].u...Oz7....9.(
    0030 - 56 52 97 03 88 5e 62 ed-a3 f6 3e fd ae 29 d1 e7   VR...^b...>..)..
    0040 - df df 95 96 46 e4 62 13-40 21 98 45 17 c8 76 ac   ....F.b.@!.E..v.
    0050 - 94 ef 33 68 01 30 57 df-c3 aa 0c cd 89 2b 6b 86   ..3h.0W......+k.
    0060 - 89 e2 0c b1 e7 1a 6d 16-11 d9 9c 06 cb 17 0a 1b   ......m.........
    0070 - 57 34 ac 88 4a 0d a5 a0-8c e2 ef 00 2b 42 13 58   W4..J.......+B.X
    0080 - 51 37 09 43 bf 43 1a af-be c5 c5 02 0d e5 ea e9   Q7.C.C..........
    0090 - 3c 85 8e 94 1c 7a 32 1b-2b 61 08 24 14 e8 c2 48   <....z2.+a.$...H
    00a0 - 4b f9 26 28 2e f3 fb b5-88 41 0c 73 e1 d3 a6 a2   K.&(.....A.s....
    00b0 - be 43 9a f7 b8 28 44 18-42 a7 3a 74 11 06 43 72   .C...(D.B.:t..Cr
    00c0 - 57 54 ac a1 43 77 2d a7-c0 53 eb 97 f8 5a a1 ff   WT..Cw-..S...Z..
    00d0 - 67 eb da 74 ea ba a8 58-7d 99 8c e0 2d eb c8 63   g..t...X}...-..c
    00e0 - 49 fa 7b ee 42 52 55 b0-6f cf 23 b5 58 8c 06      I.{.BRU.o.#.X..

    Start Time: 1732404453
    Timeout   : 7200 (sec)
    Verify return code: 0 (ok)
    Extended master secret: no
    Max Early Data: 0
---
read R BLOCK
220 smtp.gmail.com ESMTP 5b1f17b1804b1-433cde058fcsm74629515e9.3 - gsmtp
```
> ```
> openssl s_client -connect pop.gmail.com:995
> ```
```
CONNECTED(00000003)
depth=2 C = US, O = Google Trust Services LLC, CN = GTS Root R1
verify return:1
depth=1 C = US, O = Google Trust Services, CN = WR2
verify return:1
depth=0 CN = pop.gmail.com
verify return:1
---
Certificate chain
 0 s:CN = pop.gmail.com
   i:C = US, O = Google Trust Services, CN = WR2
   a:PKEY: id-ecPublicKey, 256 (bit); sigalg: RSA-SHA256
   v:NotBefore: Oct 21 08:37:55 2024 GMT; NotAfter: Jan 13 08:37:54 2025 GMT
 1 s:C = US, O = Google Trust Services, CN = WR2
   i:C = US, O = Google Trust Services LLC, CN = GTS Root R1
   a:PKEY: rsaEncryption, 2048 (bit); sigalg: RSA-SHA256
   v:NotBefore: Dec 13 09:00:00 2023 GMT; NotAfter: Feb 20 14:00:00 2029 GMT
 2 s:C = US, O = Google Trust Services LLC, CN = GTS Root R1
   i:C = BE, O = GlobalSign nv-sa, OU = Root CA, CN = GlobalSign Root CA
   a:PKEY: rsaEncryption, 4096 (bit); sigalg: RSA-SHA256
   v:NotBefore: Jun 19 00:00:42 2020 GMT; NotAfter: Jan 28 00:00:42 2028 GMT
---
Server certificate
-----BEGIN CERTIFICATE-----
MIIEVjCCAz6gAwIBAgIRAKM/wMLdK0UsCt+QlR859+kwDQYJKoZIhvcNAQELBQAw
OzELMAkGA1UEBhMCVVMxHjAcBgNVBAoTFUdvb2dsZSBUcnVzdCBTZXJ2aWNlczEM
MAoGA1UEAxMDV1IyMB4XDTI0MTAyMTA4Mzc1NVoXDTI1MDExMzA4Mzc1NFowGDEW
MBQGA1UEAxMNcG9wLmdtYWlsLmNvbTBZMBMGByqGSM49AgEGCCqGSM49AwEHA0IA
BL4exwJgvQqgPiLHndsl4us1gto9rwHbMhLe4s3NLDtVSs/K/5E5EBG6eJGbyCsj
BtAVYNCFromA3HPnlzX0g8OjggJBMIICPTAOBgNVHQ8BAf8EBAMCB4AwEwYDVR0l
BAwwCgYIKwYBBQUHAwEwDAYDVR0TAQH/BAIwADAdBgNVHQ4EFgQUbM9U7QJ2MHHz
oIwktRH1gSQh+40wHwYDVR0jBBgwFoAU3hse7XkV1D43JMMhu+w0OW1CsjAwWAYI
KwYBBQUHAQEETDBKMCEGCCsGAQUFBzABhhVodHRwOi8vby5wa2kuZ29vZy93cjIw
JQYIKwYBBQUHMAKGGWh0dHA6Ly9pLnBraS5nb29nL3dyMi5jcnQwGAYDVR0RBBEw
D4INcG9wLmdtYWlsLmNvbTATBgNVHSAEDDAKMAgGBmeBDAECATA2BgNVHR8ELzAt
MCugKaAnhiVodHRwOi8vYy5wa2kuZ29vZy93cjIvR1N5VDFONFBCcmcuY3JsMIIB
BQYKKwYBBAHWeQIEAgSB9gSB8wDxAHYAzxFW7tUufK/zh1vZaS6b6RpxZ0qwF+ys
AdJbd87MOwgAAAGSrnC8JAAABAMARzBFAiEAqQwlnXXLO81Znw+MuB8WpP4JIBkx
Xlh2fDCRwdf5eCMCIHoZMlEhapQ8g1bu4zH8wdRzn9Kfq7Xa9GKWFxW7ylaNAHcA
zPsPaoVxCWX+lZtTzumyfCLphVwNl422qX5UwP5MDbAAAAGSrnC8KgAABAMASDBG
AiEAzaKVDm32FflOnGpyvRq721G0JfDlx9OTka+NrTVTNKkCIQCeq1tW06KUcMzW
+43ZF7E/DEW30Pg8fHOTIpmmFjFxyTANBgkqhkiG9w0BAQsFAAOCAQEAfv6B4rkK
Q78Dd47at7vUjdJn1vmxeVeFRdpWOtwHH586W50oqJWO9OV04wZ6P8aysh15JuD7
u9UKM8Surtii+X8XRCYyfTW+Jbn7kOaYTe6oNSN0ZDJ/1VRSZpZJ7NR5rA4hdJze
VknhYrA7K8Kdt5jZFaA4VtY83+vgkYaol4/Na/LOo0EN86F+7aCq949jqSR7W81i
cpumaM3Tw1BMCGCZFMtWnog3hjWGjCZG8Gxs3A7gdUIHHU6L0JKbm43lGQBQTIPS
dvE54YxgPn6eauiMPOzwa0feEQtc65MU2n0hxfTe6A8VaPFlQtMvyXuvJm55Sd0E
mhHX9Gu6t5RUTw==
-----END CERTIFICATE-----
subject=CN = pop.gmail.com
issuer=C = US, O = Google Trust Services, CN = WR2
---
No client certificate CA names sent
Peer signing digest: SHA256
Peer signature type: ECDSA
Server Temp Key: X25519, 253 bits
---
SSL handshake has read 4106 bytes and written 399 bytes
Verification: OK
---
New, TLSv1.3, Cipher is TLS_AES_256_GCM_SHA384
Server public key is 256 bit
Secure Renegotiation IS NOT supported
Compression: NONE
Expansion: NONE
No ALPN negotiated
Early data was not sent
Verify return code: 0 (ok)
---
---
Post-Handshake New Session Ticket arrived:
SSL-Session:
    Protocol  : TLSv1.3
    Cipher    : TLS_AES_256_GCM_SHA384
    Session-ID: EBE1B85AF8CC247003F30DA8524559815198DBE3C5496D4DF4860CB8A7B86B0C
    Session-ID-ctx:
    Resumption PSK: 18889B012984988703AA9346A74121767F00120079EE694565A6B8AA3E1D3178609D2A4562493E84400DC713048513B5
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 172800 (seconds)
    TLS session ticket:
    0000 - 02 8c 74 fd e8 08 e3 9a-96 43 10 56 39 64 65 9c   ..t......C.V9de.
    0010 - 89 27 c3 f8 0b 4c 77 9a-14 bd fc 7e e0 42 fe 85   .'...Lw....~.B..
    0020 - 0a 93 3b 2b 32 0a 1c 93-4d fc 05 1a 7f a9 b4 46   ..;+2...M......F
    0030 - 8b 5d f4 73 57 22 11 d3-49 6b d6 ea 01 2e 4f da   .].sW"..Ik....O.
    0040 - 5f 90 be e3 06 61 64 a5-d2 83 9a fb 34 07 b6 97   _....ad.....4...
    0050 - ad 22 d8 9b 6d 39 b4 c5-dd fd 24 33 5c ea b8 a7   ."..m9....$3\...
    0060 - ef 0a ce d7 15 1a 5a 1a-3c 8f 1a d8 4f 95 c9 54   ......Z.<...O..T
    0070 - 34 c2 bd 22 69 b9 ed 2d-1b 2c 15 3a 2c cc 92 81   4.."i..-.,.:,...
    0080 - ab cb f5 3e b7 83 e7 ae-ae e2 b8 52 27 f3 a1 ec   ...>.......R'...
    0090 - 28 a6 46 46 29 ca 1d 96-64 3b 07 35 21 0e 2e 83   (.FF)...d;.5!...
    00a0 - 28 24 f7 42 9e d9 51 07-84 13 dd 16 88 be 8c 02   ($.B..Q.........
    00b0 - df 38 e1 64 ba 0c e0 68-1f 29 8e 2d dc 8b bb 58   .8.d...h.).-...X
    00c0 - e0 65 06 90 2c 19 6e d1-11 aa 75 b9 5d de a2 0d   .e..,.n...u.]...
    00d0 - ce 0e 83 28 c6 03 47 e8-f7 79 94 d4 a9 0a 2b 08   ...(..G..y....+.
    00e0 - 07 66 13 16 42 52 55 8d-5d 6c 75 8b e4 d7 09      .f..BRU.]lu....

    Start Time: 1732404499
    Timeout   : 7200 (sec)
    Verify return code: 0 (ok)
    Extended master secret: no
    Max Early Data: 0
---
read R BLOCK
---
Post-Handshake New Session Ticket arrived:
SSL-Session:
    Protocol  : TLSv1.3
    Cipher    : TLS_AES_256_GCM_SHA384
    Session-ID: 6D7276F48A804C493E8F574B36BC68777550D37F09EBB4E96DA8F83A551D330B
    Session-ID-ctx:
    Resumption PSK: F1818015500BECAC16386BACD0D42D1C5B21141FAD91F5FAA079D8362C8889B628587417906215DC2E1FB3166BB7E1AE
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 172800 (seconds)
    TLS session ticket:
    0000 - 02 8c 74 fd e8 08 e3 9a-96 43 10 56 39 64 65 9c   ..t......C.V9de.
    0010 - 8f 4d 2a dc 88 b0 88 e5-42 67 4e ec cd 66 5b 9f   .M*.....BgN..f[.
    0020 - ec 0f 8d b5 e5 ed ee 56-62 32 69 7f 59 d1 cb 07   .......Vb2i.Y...
    0030 - 09 ae 16 cc 31 12 db d3-bb b8 ff 59 26 d8 ab 98   ....1......Y&...
    0040 - e9 83 77 08 8f 5c d6 55-0b f9 24 09 b9 02 bc 36   ..w..\.U..$....6
    0050 - 46 a9 01 7a b8 04 e5 34-6f 18 fb ee b6 88 12 cd   F..z...4o.......
    0060 - df 02 27 3b 95 de 42 f4-33 30 eb 0e 6b 11 f2 43   ..';..B.30..k..C
    0070 - 09 db 35 fa 58 39 f5 42-01 d2 0e f1 c4 0a 21 36   ..5.X9.B......!6
    0080 - 07 fe 6b b1 7d ac 46 1e-13 4c 6d 24 8c 50 cf fe   ..k.}.F..Lm$.P..
    0090 - 82 41 99 cb c9 4a 54 1d-4b 2b b8 e3 ad 00 cb 36   .A...JT.K+.....6
    00a0 - 4a cb 0f ed d4 e9 ac 5c-4b 44 14 01 80 ca 89 74   J......\KD.....t
    00b0 - 03 ad d8 7c c2 16 da 89-b9 d0 6b 8c 73 d4 aa 85   ...|......k.s...
    00c0 - 83 9b e7 1a 7a bf 0d b9-b1 02 63 c6 7d b1 e4 a4   ....z.....c.}...
    00d0 - ba 34 84 65 89 00 c3 3b-3f 5b fd c2 ae 48 21 53   .4.e...;?[...H!S
    00e0 - c3 f7 55 83 42 52 55 45-a1 47 5c 39 8c 36 2e      ..U.BRUE.G\9.6.

    Start Time: 1732404499
    Timeout   : 7200 (sec)
    Verify return code: 0 (ok)
    Extended master secret: no
    Max Early Data: 0
---
read R BLOCK
+OK Gpop ready for requests from 163.117.172.176 z10mb83089744wro
```


### Ejercicio 16
> Vamos ahora a conectarnos al puerto submission de ESTMP en `smtp.gmail.com`. Haga `telnet` al puerto `587`, haga el `EHLO` y luego intente enviar un correo. ¿Qué respuesta recibe?
```
$ telnet smtp.gmail.com 587
Trying 74.125.133.109...
Connected to smtp.gmail.com.
Escape character is '^]'.
220 smtp.gmail.com ESMTP ffacd0b85a97d-3825fbedfccsm6327686f8f.101 - gsmtp
EHLO lab.it.uc3m.es
250-smtp.gmail.com at your service, [163.117.172.176]
250-SIZE 35882577
250-8BITMIME
250-STARTTLS
250-ENHANCEDSTATUSCODES
250-PIPELINING
250-CHUNKING
250 SMTPUTF8
MAIL FROM: feik@lab.it.uc3m.es
530-5.7.0 Must issue a STARTTLS command first. For more information, go to
530-5.7.0  https://support.google.com/a/answer/3221692 and review RFC 3207
530 5.7.0 specifications. ffacd0b85a97d-3825fbedfccsm6327686f8f.101 - gsmtp
STARTTLS
220 2.0.0 Ready to start TLS
Connection closed by foreign host.
```

### Ejercicio 17
> Puede usar `openssl` para conectarse a los puertos en claro de los protocolos de correo y enviar el comando `STARTTLS` / `STLS` para iniciar la negociación TLS. Hágalo conectándose al puerto de submission de ESTMP en `smtp.gmail.com` del siguiente modo: `openssl s_client -starttls smtp -connect smtp.gmail.com:587 -debug` (de forma similar se haría para otros protocolos). ¿Qué respuesta recibe ahora si intenta hacer `EHLO` y enviar un correo?
```
$ openssl s_client -starttls smtp -connect smtp.gmail.com:587
CONNECTED(00000003)
...
250 SMTPUTF8
EHLO lab.it.uc3m.es
...
read R BLOCK
250-smtp.gmail.com at your service, [163.117.172.176]
250-SIZE 35882577
250-8BITMIME
250-AUTH LOGIN PLAIN XOAUTH2 PLAIN-CLIENTTOKEN OAUTHBEARER XOAUTH
250-ENHANCEDSTATUSCODES
250-PIPELINING
250-CHUNKING
250 SMTPUTF8
```