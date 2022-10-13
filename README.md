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


