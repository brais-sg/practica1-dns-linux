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



```