---
layout: post
section-type: post
title: Kotlin + Docker - Contenerizando un Hola Mundo
category: kotlin
overview: Aprende a crear una imagen docker del Hola Mundo en Kotlin 
image: docker_kotlin.png
tags: ['kotlin', 'docker' ]
---

# Introducción
Hace algunos años que dejé de desarrollar software de manera profesional, pues aunque en mi trabajo mis funciones siguen estando en el área de TI, son a un nivel más ejecutivo, sin embargo, como buenos Líderes TI, nunca debemos dejar de aprender. Este no es un tutorial desde cero sobre docker y supone que un conocimiento básico de gradle.

# Creando la App Kotlin
Para mantener la simplicidad del ejercicio, vamos a crear una imagen docker con una sencilla app en kotlin que haga el Hola Mundo, pero no compilaremos la app kotlin dentro del contenedor, más bien desplegaremos la app ya empaquetada. 

1. Creamos una carpeta
1. Nos movemos a la carpeta creada
1. Creamos la app kotlin con gradle

Usaremos gradle para crear la app kotlin

```gradle
❯ mkdir hola-kotlin
❯ cd hola-kotlin
❯ gradle init --dsl kotlin
Starting a Gradle Daemon (subsequent builds will be faster)

Select type of project to generate:
  1: basic
  2: application
  3: library
  4: Gradle plugin
Enter selection (default: basic) [1..4] 2

Select implementation language:
  1: C++
  2: Groovy
  3: Java
  4: Kotlin
  5: Scala
  6: Swift
Enter selection (default: Java) [1..6] 4
```
Ahora vamos a validar que nuestro magnífico Hola Mundo funciona correctamente!

```gradle
❯ ./gradlew run

> Task :app:run
Hello World!

BUILD SUCCESSFUL in 3s
```
# Empaquetando la app
Como mencionamos, vamos a mandar la app ya empaquetada a nuestra imagen docker, es decir que no vamos compilar ni bajar dependencias desde la imagen. Por lo tanto vamos a empaquetar la app en un TAR

```gradle
❯ ./gradlew distTar
```

Ahora, si miramos dentro de **app/build/distributions** ahi estará nuestro archivo tar

# Creando el Dockerfile
Vamos a crear nuestro archivo Dockerfile de una capa base que contenga una versión reducida de la JRE ya que no vamos a compilar nada, recordemos que vamos a mandar nuestra app ya compilada.

```docker
# Debido a que no vamos a compilar (pues ya lo hicimos) solo a ejecutar, usaremos una versión ligera de la JRE
FROM openjdk:8-jre-alpine

# Creamos un directorio /kotlin dentro de  nuestra imagen
RUN mkdir /kotlin

# Descomprimimos en la carpeta /kotlin que creamos arriba, el tar que generamos previamenre con ./gradlew distTar
ADD app/build/distributions/app.tar /kotlin

# WORKDIR es como hace un comando cd, es decir, nos pasamos a /kotlin/app
WORKDIR /kotlin/app

# Ejecutamos la app
CMD ["bin/app"]
```
Hasta este punto, nuestra estructura de carpetas debe ser similar a esto:

```
.
├── Dockerfile
├── app
├── build
├── gradle
├── gradlew
├── gradlew.bat
└── settings.gradle.kts
```

# Compilando y ejecutando la imagen docker

Nuestro último paso es compilar la imagen docker, así que para ello ejecutamos ``docker build`` y posteriormente validamos que nuestra imagen ha sido creada:

```gradle
❯ docker build -t app-kotlin .
❯ docker images
REPOSITORY              TAG       IMAGE ID       CREATED          SIZE
app-kotlin              latest    b5e01d6c2363   18 minutes ago   89.7MB
```

Ahora solo corremos con ``docker run``

```
❯ docker run --rm  app-kotlin
```

![Alt Text](static/img/docker-run.gif)

En otros tutoriales escribiré como conectar con una BD Mongo, donde obviamente la base de datos estará en otro contenedor independiente.
