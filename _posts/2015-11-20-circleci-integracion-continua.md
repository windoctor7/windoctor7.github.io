---
layout: post
title: CircleCI Integración continua en la nube
tags:
- integracion continua
- agile
---

En [circleci.com](http://circleci.com) se ofrece un servicio gratuito para la integración continua en la nube, ideal para
 desarrolladores independientes que no requieren de grandes necesidades, quizá para proyectos personales como el de [AdoraiTunes](https://github.com/windoctor/AdoraiTunes)

Cada vez que se hace un push al repositorio, circleci correrá las pruebas unitarias y nos mostrará los resultados.

![circleci](https://raw.githubusercontent.com/windoctor/windoctor.github.io/master/static/img/circleci_2015-11-20_2212.png)

Por default y al momento de escribir este articulo, ***circleci*** compila con la versión de java 7, pero podemos escoger la versión incluyendo en la carpeta root de nuestro repositorio el archivo circle.yml con el siguiente contenido:

``` yaml
machine:
	java:
		version: oraclejdk8
```

Finalmente es destacable el soporte para diferentes bases de datos, entre ellas MongoDB, en tus pruebas debes apuntar a localhost, sin usuario ni contraseña.

Los invito a revisar la página, existen varias opciones, circleci me gusto por su simplicidad y sin duda para varios proyectos personales es suficiente.
