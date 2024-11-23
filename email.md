# Práctica de correo electrónico
En esta práctica vamos a probar los protocolos relacionados con el correo electrónico. A lo largo de esta práctica, vamos a suponer que su login es `0xxxxxx`.

## Ejercicios

### Ejercicio 1
Todos los puestos del laboratorio tienen un servidor de correo lanzado. Averigüe, haciendo un `telnet` a su propia máquina y al puerto que corresponda, si el servidor de correo soporta o no ESMTP. En caso afirmativo, diga qué opciones de ESMTP soporta. Consulte las RFCs [1854](http://tools.ietf.org/html/rfc1854) y [1870](http://tools.ietf.org/html/rfc1870) para entender en qué consisten las opciones `PIPELINING` y `SIZE`.

```
telnet localhost 25
```
