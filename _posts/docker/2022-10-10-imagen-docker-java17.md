---
layout: post
section-type: post
title: Reduciendo el tamaño de la JVM en imagen docker
category: docker
overview: En este tutorial aprenderás como contenerizar un microservicio en spring boot con Docker y Java 17 creando una JRE personalizada cuya imagen sea menor a 90MB.
---

# Introducción
En este tutorial vamos a utilizar Java 17 y Gradle, así que para un correcto manejo de versiones haremos uso de [sdkman](https://sdkman.io). Su instalación es muy sencilla:

   ``curl -s "https://get.sdkman.io" | bash``

Vamos a instalar el OpenJDK 17 mediante la distribución de [Amazon Corretto](https://aws.amazon.com/es/corretto) y Gradle 7.5.1 que es la última versión al momento de escribir este tutorial. Despues de instalar ambas versiones, nos aseguramos que en efecto así haya sido:

``` 
❯ sdk install java 17.0.5-amzn
❯ sdk install gradle 7.5.1

❯ java -version
❯ gradle --v

```

Si quieres ver como instalar diferentes versiones y cambiar entre ellas, te dejo este [link a otro blog](https://towardsdatascience.com/install-and-run-multiple-java-versions-on-linux-using-sdkman-858571bce6cf)

# Creando el microservicio
Mediante gradle creamos la estructura básica de nuestro pequeño microservicio

```
❯ gradle init --type java-application
```
Sustituimos el contenido del archivo ``build.gradle`` por el [siguiente](https://gist.github.com/windoctor7/76828d550cee6031f691781314eec504). De hecho, también hay que sustituir los archivos [App.java](https://gist.github.com/windoctor7/3ed408a9eea66a890375b20b5db4873f) y [App.groovy](https://gist.github.com/windoctor7/42f6907485239fd3aaad45b2bedfc2af) :D

Ahora hacemos un build y bootRun para comprobar que todo está OK

```
❯ gradle build --info
❯ gradle bootRun --info
```
Y en nuestro navegador accedemos al endpoint de actuator ``http://localhost:8080/actuator/``

# Los módulos de Java 9
A partir de Java 9 existe el concepto de "Modules" en el cual no entraré en detalle, pero puedes ver una explicación [aquí](https://www.arquitecturajava.com/java-9-modules/).

Con esta herramienta básicamente crearemos una JRE personalizada para nuestro microservicio que vamos a contenerizar con docker mas adelante, lo cuál hará que sea mas ligero pues no vamos a exportar toda la JRE, sino solo aquellos módulos que requiramos.

Pero te preguntarás ¿Cómo saber que módulos requiere mi microservicio? Para ello usaremos [jdeps](https://docs.oracle.com/javase/9/tools/jdeps.htm#JSWOR690) que ya viene por default con nuestro OpenJDK que instalamos en pasos previos mediante sdkman. 

Ejecutamos los siguientes comandos y al final obtendremos una lista de módulos que nuestro pequeño microservicio utiliza.

```java
❯ gradle installDist
❯ jdeps --print-module-deps --ignore-missing-deps --recursive --multi-release 17 --class-path="./app/build/install/app/lib/*" --module-path="./app/build/install/app/lib/*" ./app/build/install/app/lib/app-0.1.0-plain.jar
```
Con la lista de módulos que se nos despliega, procedemos a utilizar la herramienta jlink que también ya viene por default en nuestro OpenJDK:

```java
jlink --verbose \
--verbose \
--add-modules java.base,java.desktop,java.instrument,java.management.rmi,java.naming,java.prefs,java.scripting,java.security.jgss,java.sql,jdk.httpserver,jdk.jfr,jdk.unsupported \
--strip-debug \
--no-man-pages \
--no-header-files \
--compress=2 \
--output custom_jre
```
Nuestra JRE personalizada está en la carpeta creada de nombre ``custom_jre``, incluso si navegamos hasta la carpeta ``bin`` veremos que no tenemos la tan conocida herramienta ``javac``, muestra que no se trata de un JDK sino solo de una JRE :)

Aunque no vamos a utilizar esta carpeta de custom_jre, este paso nos sirvió para entender lo que hace jlink ya que ahora lo usaremos al crear nuestra imagen docker, así que por favor elimina la carpeta:

```
rm -rf custom_jre
```

# Creando nuestra imagen docker
Vamos a utilizar la distribución Alpine de Linux como el sistema operativo base de nuestra imagen, después desde nuestro ``Dockerfile`` indicaremos crear una JRE personalizada como lo hicimos en el paso anterior y copiarla dentro de ``/opt/jre-17-minimal`` (ojo que es la ruta dentro de nuestra distribución Alpine). 

<script src="https://gist.github.com/windoctor7/3782b1a9a08129e13e427b43549d9418.js"></script>

Este sería el ``Dockerfile`` que debemos crear en nuestra carpeta del proyecto.

```bash
❯ ll
total 40
-rw-r--r--  1 aromo  staff   788B Oct 26 23:18 Dockerfile
drwxr-xr-x  7 aromo  staff   224B Oct 26 22:00 app
drwxr-xr-x  3 aromo  staff    96B Oct 26 21:53 gradle
-rwxr-xr-x  1 aromo  staff   8.0K Oct 26 21:53 gradlew
-rw-r--r--  1 aromo  staff   2.8K Oct 26 21:53 gradlew.bat
-rw-r--r--  1 aromo  staff   388B Oct 26 21:54 settings.gradle
```

# Construyendo nuestra imagen
Con nuestro ``Dockerfile`` creado, solo nos basta hacer un build

```
docker build -t windoctor7/mso-java-17:0.0.1 .
docker images
```

Podemos ver que nuestra imagen pesa aprox 90MB y no +300MB como normalmente ocurre.

Finalmente ejecutamos el contenedor y accedemos al ``http://localhost:8080/actuator/`` 

```
docker run -p 8080:8080 windoctor7/mso-java-17:0.0.1
```




