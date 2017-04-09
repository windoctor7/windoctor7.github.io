---
layout: post
title: Servicios REST con Spring Boot
source: https://github.com/windoctor/SpringBoot-Ejemplos
category: cookbook
overview: Video que muestra como desarrollar un sencillo servicio web tipo REST usando Spring Boot.
tags:
- Spring Boot
- Java Web
- REST
---

[Spring Boot](http://projects.spring.io/spring-boot/){:target="_blank"} es un sub proyecto de [Spring](https://spring.io/){:target="_blank"} que tiene como objetivo facilitar la creación de proyectos Spring, quitándo lo tedioso que resulta la configuración con XML.

Realicé el siguiente video para dar una muy breve introducción a Spring Boot mediante un sencillo servicio REST.

### Entorno
-- MacOS 10.11 El Capitan
-- Gradle 2.8
-- IntelliJ IDEA 15.0 Community Edition

Es importante aclarar que las aplicaciones con Spring Boot utilizan la especificación [Servlet 3.0](https://community.oracle.com/docs/DOC-983211){:target="_blank"} por lo que el servidor de aplicaciones o contenedor de servlets debe soportar esta especificación, en caso de tener la necesidad de desplegar una aplicación Spring Boot en un servidor que solamente soporta Servlet 2.5, será necesario agregar configuraciones adicionales que van más allá de este tutorial, quizá más adelante publique algo al respecto.

El código fuente del ejemplo está disponible en GitHub.

[![IMAGE ALT TEXT HERE](http://img.youtube.com/vi/lipk3ZRGC_E/0.jpg)](https://www.youtube.com/watch?v=lipk3ZRGC_E){:target="_blank"}

### Referencias
[http://spring.io/guides/tutorials/bookmarks/](http://spring.io/guides/tutorials/bookmarks/){:target="_blank"}