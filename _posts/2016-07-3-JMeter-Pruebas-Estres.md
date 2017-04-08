---
layout: post
title: Pruebas de estrés de software con JMeter
tags:
- JMeter
- Pruebas Software
- Java
---

Dentro del ciclo de desarrollo de software, uno de los puntos menos importantes por nosotros los desarrolladores son las pruebas de estrés. Resulta bastante el crear pruebas unitarias como para que encima de todo dediquemos tiempo para someter a nuestra aplicación a una carga de estrés y tener idea sobre su comportamiento bajo varias peticiones concurrentes.


<img style="float: right;" src="http://3.bp.blogspot.com/_ATtgRW3jaI0/StXdAx_8reI/AAAAAAAACbQ/9_WBO8lsYaM/w1200-h630-p-nu/HeaderJMeterWebservices.png"/> 
[JMeter](http://tapestry.apache.org/) es una herramientas que nos permite someter a nuestra aplicación a pruebas de estrés, es decir, podemos simular en nuestros entornos de desarrollo la carga de peticiones concurrentes que nuestra aplicación podría tener en determinado momento en el entorno productivo.

Lo único que necesitamos es [descargar](http://jmeter.apache.org/download_jmeter.cgi) la última versión de JMeter, que al momento de escribir este artículo es la 3.0 y requiere Java 7 o superior para ejecutarse.

###  Sometiendo el proyecto AdoraiTunes a pruebas de estrés

En este tutorial voy a mostrar como realizar 100 peticiones concurrentes usando mi proyecto que puedes revisar en este mismo blog. La idea es simple, mandar 100 peticiones de autenticación a la aplicación.

Lo primero es descargar JMeter, descomprimir el archivo y ejecutar el ``ApacheJMeter.jar``

Básicamente debemos seguir estos pasos:

-- ``Crear el grupo de hilos a utilizar que lanzarán las peticiones``
<img style="float: center;" src="https://raw.githubusercontent.com/windoctor/windoctor.github.io/master/static/img/2016-07-03_0042.png"/> 

<div>&nbsp;</div>

-- ``Configurar a los 100 threads que serán los usuarios que realizarán la petición de autenticación.``
<img style="float: center;" src="https://raw.githubusercontent.com/windoctor/windoctor.github.io/master/static/img/2016-07-03_0856.png"/> 

    En la opción "periodo de subida" se indican los segundos que tardará nuestro grupo de 100 hilos en realizar las peticiones, esto es, 100 usuarios / 25 segundos = 4 usuarios concurrentes por segundo.

-- ``Crear la petición HTTP``
<img style="float: center;" src="https://raw.githubusercontent.com/windoctor/windoctor.github.io/master/static/img/2016-07-03_0045.png"/> 

<div>&nbsp;</div>

<img style="float: center;" src="https://raw.githubusercontent.com/windoctor/windoctor.github.io/master/static/img/2016-07-03_0046.png"/> 

<div>&nbsp;</div>

    Podemos ver dentro de los parámetros a configurar que se trata de una petición POST, la ruta y los parámetros de la petición los obtuve con la ayuda de Firebug, simplemente hice una autenticación manual y desde las herramientas de desarrollador del navegador pude saber como se enviaba la petición para así enviarlas desde JMeter. La imagen de abajo muestra esto último.
        
<div>&nbsp;</div>

<img style="float: center;" src="https://raw.githubusercontent.com/windoctor/windoctor.github.io/master/static/img/2016-07-03_0907.png"/> 

<div>&nbsp;</div>

        
-- ``Crear el árbol de resultados donde podamos ver la respuesta del servidor``
<img style="float: center;" src="https://raw.githubusercontent.com/windoctor/windoctor.github.io/master/static/img/2016-07-03_0047.png"/> 

Finalmente estamos listos para correr la prueba de estrés con las teclas Ctrl+R y ver los resultados

<div>&nbsp;</div>
<img style="float: center;" src="https://raw.githubusercontent.com/windoctor/windoctor.github.io/master/static/img/2016-07-03_0101.png"/>

<div>&nbsp;</div>

En este punto es bueno señalar que antes de ejecutar la prueba de estrés, las conexiones que habían en la base de datos mongo eran las siguientes:

<div>&nbsp;</div>
<img style="float: center;" src="https://raw.githubusercontent.com/windoctor/windoctor.github.io/master/static/img/2016-07-03_0059.png"/>

<div>&nbsp;</div>

En cuanto se ejecutó la prueba, las conexiones comenzaron a incrementar. 

<div>&nbsp;</div>
<img style="float: center;" src="https://raw.githubusercontent.com/windoctor/windoctor.github.io/master/static/img/2016-07-03_0100.png"/>








