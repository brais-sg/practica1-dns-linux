# Práctica 1 DNS con Docker y Linux
Práctica 1 para la asignatura SRI
Por Brais Solla González

# Imágenes a usar
Para esta práctica vamos a usar las imágenes de bind9 y alpine Linux


[Imagen de bind9](https://hub.docker.com/r/internetsystemsconsortium/bind9)


[Imagen de alpine](https://hub.docker.com/_/alpine)


# Configuración de los volúmenes

Para la creación del servidor, vamos a establecer los siguientes directorios usando volúmenes

1. Volúmen para los ficheros de configuración: ```/etc/bind``` hacia ```conf```
2. Volúmen para las zonas del servidor DNS: ```/var/lib/bind``` hacia ```zonas```


# Creando el fichero de configuración para bind9

Para esto, vamos a dividir la configuración en dos ficheros: ```named.conf.local``` y ```named.conf.options``` que incluiremos desde ```named.conf```

El fichero ```named.conf.local``` contendrá la configuración de las zonas del DNS y el fichero ```named.conf.options``` las opciones del servidor bind9

En el archivo de configuración principal, debemos incluir los otros dos:
```
include "/etc/bind/named.conf.options";
include "/etc/bind/named.conf.local";
```

Tras esto, configuramos las opciones de bind9 en ```named.conf.options```
```conf
options {
        directory "/var/cache/bind";

        forwarders {
            8.8.8.8;
            8.8.4.4;
        };
        forward only;

        listen-on { any; };
        listen-on-v6 { any; };
        allow-query {
            any;
        };
};
```

Posteriormente, configuramos una zona en el fichero ```named.conf.local```
```conf
zone "asircastelao.int" {
        type master;
        file "/var/lib/bind/db.asircastelao.int";
        allow-query {
            any;
        };
};
```

# Modificando el fichero con la zona
Para esto, creamos dentro de zona el fichero ```db.asircastelao.int``` referenciado previamente desde ```named.conf.local```

En este fichero, definiremos todos los registros para la zona db.asircastelao.int

```conf
$TTL    3600
@       IN      SOA     ns.asircastelao.int. a.asircastelao.int. (
                   2022051001           ; Serial
                         3600           ; Refresh [1h]
                          600           ; Retry   [10m]
                        86400           ; Expire  [1d]
                          600 )         ; Negative Cache TTL [1h]
;
@       IN      NS      ns.asircastelao.int.
@       IN      A       10.1.0.2

ns      IN      A       10.1.0.254

mail    IN      MX      10.1.0.2
test    IN      A       10.1.0.2

 
alias   IN      CNAME   test
correo  IN      CNAME   mail
text    IN      TXT     "Esta es una entrada de texto"
```

# Creando la red externa para conectar ambas máquinas
Para esto debemos utilizar el comando ```docker network create``` para crear la red, donde nuestro DNS será la máquina que eligiremos como la puerta de enlace


# Configurando el docker-compose y lanzando ambos contenedores
Creamos el siguiente archivo ```docker-compose.yml```

```
version: "3.3"
services:
  asir_bind9:
    container_name: asir_bind9
    image: internetsystemsconsortium/bind9:9.16
    networks:
      bind9_subnet:
        ipv4_address: 10.1.0.254
    volumes:
      - /home/asir2a/practica1-dns-linux/conf:/etc/bind
      - /home/asir2a/practica1-dns-linux/zonas:/var/lib/bind
  asir_cliente:
    container_name: asir_cliente
    image: alpine
    networks:
      - bind9_subnet
    stdin_open: true
    tty: true
    dns:
      - 10.1.0.254 # Esto es el DNS a usar
networks:
  bind9_subnet:
    external: true
```

