---
layout: post
section-type: post
title: Desarrollo rápido con Kotlin y Spring 5
category: spring
overview: En este tutorial se explica el concepto de Programación Reactiva de forma simple y clara. Al final desarrollamos un ejemplo web usando WebFlux de Spring 5 y el soporte para MongoDB y Thymeleaf reactivo.
tags: ['spring', 'spring boot' ]
source: https://github.com/windoctor7/codigo-tutoriales-blog/tree/master/spring-webflux-2
---

![logo kotlin](https://github.com/windoctor7/windoctor7.github.io/raw/master/static/img/logo_Kotlin.png)

---

## Introducción a Kotlin

[Kotlin](https://kotlinlang.org/docs/reference/basic-syntax.html) es un lenguaje de reciente aparición que rápidamente llamó la atención de los desarrolladores Android. Kotlin fué diseñado por [JetBrains](https://www.jetbrains.com), la empresa que está detrás del que a mi opinión es por mucho el mejor IDE para Java.

Kotlin no solo es un lenguaje para la JVM, también tenemos [Kotlin/JS](https://kotlinlang.org/docs/tutorials/javascript/kotlin-to-javascript/kotlin-to-javascript.html) el cual nos permite compilar código fuente escrito en Kotlin a JavaScript. Por supuesto, Kotlin también corre sobre Android y de hecho Google ya [anunció](https://blog.jetbrains.com/kotlin/2017/05/kotlin-on-android-now-official/) soporte oficial, de hecho a partir de Android Studio 3.0, kotlin ya viene incluido como lenguaje y no tienes que instalar nada adicional.

La forma más rápida de iniciar con Kotlin es descargar [IntelliJ IDEA](www.jetbrains.com/idea/download/). Puedes descargar la versión comunitaria que si trae soporte para Kotlin.

Creamos un proyecto Kotlin,

```
File --> New --> Project --> Kotlin/JVM
```
    
Una vez creado, sobre la carpeta **src** click derecho New --> Kotlin File/Class y creamos una clase.

En kotlin

```kotlin
fun main(args: Array<String>) {
    //La palabra "new" no existe
    val student = Student(1,"Ascari Romo",10.5)
    println(student)
}
```

