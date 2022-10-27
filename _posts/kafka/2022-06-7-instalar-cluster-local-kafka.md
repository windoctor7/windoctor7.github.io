---
layout: post
section-type: post
title: Instalación básica de un clúster local de Apache Kafka
category: kafka
overview: Apache Kafka se ha convertido en un estándar 
tags: ['spring', 'spring boot' ]
image: https://raw.githubusercontent.com/windoctor7/windoctor7.github.io/master/static/img/pwa.png
url: http://windoctor7.github.io
source: https://github.com/windoctor7/codigo-tutoriales-blog/tree/master/spring-pwa
---

# Introducción
En este tutorial vamos a realizar la instalación y configuración básica de un clúster local de Apache Kafka, es decir, tendremos varias instancias de kafka corriendo sobre la misma máquina simulando un clúster real. A cada una de estas instancias de kafka ejecutándose, les llamaremos Broker. Para este tutorial hemos utilizado Mac OS, sin embargo en un Linux seguramente funcionará sin ningún problema.

La instalación es realmente muy sencilla, solo debemos descargar los archivos binarios desde su [página oficial](https://kafka.apache.org). Al momento de escribir este tutorial, la última versión estable es la **3.2.0** compilada con Scala 2.13 que es la que te recomiendo descargar.

# Zookeeper
Una vez que hemos descargado y descomprimido los archivos de Apache Kafka, vamos a situarnos en el directorio **/INSTALACION_KAFKA/bin** y a ejecutar zookeeper mediante el siguiente comando:

```zaa
./zookeeper-server-start.sh ../config/zookeeper.properties
```

![run-zookeeper](static/img/run-zookeeper.png)

[Zookeeper](https://zookeeper.apache.org) es un servicio centralizado del cual Apache Kafka hace uso para el manejo de ambientes distribuidos. Zookepeer es quien se va a encargar de mantener en armonía a los diferentes brokers de kafka. Zookeeper es como la torre de control de un aeropuerto, se encargará de coordinar a los brokers kafka. En este tutorial no entraremos en mayor detalle, basta saber que para poder ejecutar kafka, primero debemos tener corriendo zookeeper, que fue el paso anterior que realizaste.

Dentro de la carpeta config, encontraremos el archivo de configuración ``zookeeper.properties`` , en donde podremos cambiar el puerto o ruta donde se escriban los logs. Puedes abrirlo con algún editor de texto y revisar su contenido.

# Ejecutando Apache Kafka
Para ejecutar Kafka ya no requerimos descargar nada adicional, con lo que descargamos al inicio de este tutorial es suficiente, lo único que necesitamos es situarnos en la carpeta **bin** y ejecutar el daemon de kafka pasándole el archivo de configuración ``server.properties`` que se encuentra en la carpeta **config**

````bash
cd bin
./kafka-server-start.sh ../config/server.properties
````

Recuerda que para iniciar Kafka, debemos tener corriendo a Zookeeper primeramente. Tras ejecutar el comando anterior, en el log podremos observar entre muchas otras líneas, algo como esto:

````bash
[2022-06-07 17:39:40,839] INFO Kafka version: 3.2.0 (org.apache.kafka.common.utils.AppInfoParser)
[2022-06-07 17:39:40,840] INFO Kafka commitId: 38103ffaa962ef50 (org.apache.kafka.common.utils.AppInfoParser)
[2022-06-07 17:39:40,840] INFO Kafka startTimeMs: 1654641580834 (org.apache.kafka.common.utils.AppInfoParser)
[2022-06-07 17:39:40,841] INFO [KafkaServer id=0] started (kafka.server.KafkaServer)
````
# Configurando el clúster
Ahora que ya tenemos un broker kafka ejecutándose, vamos a simular un clúster local, para ello requerimos clonar el archivo de configuración de kafka, **server.properties** que se encuentra en la carpeta config:

```bash
cp server.properties server-1.properties
cp server.properties server-2.properties
```
De esta forma tendremos 3 archivos: 

1. server.properties (este es el archivo original y sobre el cual ya tenemos a nuestra primer instancia de kafka corriendo)
1. server-1.properties
1. server-2.properties

Ahora vamos a modificar los archivos server-1 y server-2 , localizamos las siguientes líneas y actualizamos sus valores:

````
#broker.id=0
broker.id=1

#listeners=PLAINTEXT://:9092
listeners=PLAINTEXT://:9093

#log.dirs=/tmp/kafka-logs
log.dirs=/tmp/kafka-logs-1
````

Como podemos ver, a cada una de las líneas anteriores, le estamos cambiando un valor, desde el id del broker, pasando por el puerto donde correrá la instancia de kafka y el nombre del archivo donde se escribirán los logs.

Para el archicvo ``server-2.properties`` hacemos lo mismo de tal forma que podemos tener algo como esto:

````
broker.id=2
listeners=PLAINTEXT://:9094
log.dirs=/tmp/kafka-logs-2
````

Finalmente, una vez que guardamos los cambios en nuestros 2 archivos, estamos listos para ejecutar otras 2 instancias de kafka pasándole su respectivo archivo de configuración:

```
./kafka-server-start.sh ../config/server-1.properties
./kafka-server-start.sh ../config/server-2.properties
```

De esta forma podemos ejecutar tantos brokers de kafka como lo necesitemos. 

# Algunos puntos teóricos...
Ya estamos listos para publicar algunos mensajes, pero antes de ello quiero mencionar brevemente sobre algunos puntos teóricos pero muy importantes, **las particiones** y el **factor de replicación**
