---
layout: post
section-type: post
title: Desarrollando en Kotlin y Spring 5
category: kotlin
overview: En este tutorial nos conectaremos a MongoDB mediante Spring Data y lo haremos utilizando Kotlin como lenguaje de programación en lugar de Java.
tags: ['spring', 'spring boot', 'kotlin' ]
source: https://github.com/windoctor7/codigo-tutoriales-blog/tree/master/spring-kotlin
---

![logo kotlin](https://github.com/windoctor7/windoctor7.github.io/raw/master/static/img/logo_Kotlin.png)

---

## Introducción

[Kotlin](https://kotlinlang.org/docs/reference/basic-syntax.html) es un lenguaje de reciente aparición que rápidamente llamó la atención de los desarrolladores Android. Kotlin fué diseñado por [JetBrains](https://www.jetbrains.com), la empresa que está detrás del que a mi opinión es por mucho el mejor IDE para Java.

Kotlin no solo es un lenguaje para la JVM, también tenemos [Kotlin/JS](https://kotlinlang.org/docs/tutorials/javascript/kotlin-to-javascript/kotlin-to-javascript.html) el cual nos permite compilar código fuente escrito en Kotlin a JavaScript. Por supuesto, Kotlin también corre sobre Android y de hecho Google ya [anunció](https://blog.jetbrains.com/kotlin/2017/05/kotlin-on-android-now-official/) soporte oficial, de hecho a partir de Android Studio 3.0, kotlin ya viene incluido como lenguaje y no tienes que instalar nada adicional.

Con Kotlin para la JVM podemos escribir aplicaciones de escritorio o web. En este tutorial aprenderemos como escribir aplicaciones web utilizando el fantástico Spring Boot 2 que usa Spring 5.

Desde [start.spring.io](https://start.spring.io/#!language=kotlin&type=gradle-project) podemos generar rápidamente un proyecto Spring Boot 2 con soporte para Kotlin. Asegurate de elegir Spring Boot 2, que al momento de escribir este tutorial está en su versión 2.0.0 M5 y como dependencia elige "Reactive Web".

Si te aburre la teoría, tal vez quieras saltarte la siguiente sección e ir directo a [Kotlin y Spring](#kotlin-y-spring)

---

## Kotlin y Gradle

Una vez que has creado el proyecto desde [start.spring.io](https://start.spring.io/#!language=kotlin&type=gradle-project) revisa el archivo **build.gradle** generado y dentro de la sección buildscript verás lo siguiente:

    classpath("org.jetbrains.kotlin:kotlin-gradle-plugin:${kotlinVersion}")
    classpath("org.jetbrains.kotlin:kotlin-allopen:${kotlinVersion}")
    
**kotlin-gradle-plugin** => Se encarga de compilar el código fuente de Kotlin.

**kotlin-allopen** => Este plugin permite hacer que las clases marcadas con una anotación específica sea abierta sin la necesidad de marcarla como "open". En Kotlin todas las clases son *final* y esto causa un problema para determinados frameworks utilizados en Java. Por ello, este plugin nos permite indicar las anotaciones que si una clase tiene, esta clase será marcada automáticamente como open.

De esta forma, puedes aplicar el plugin e indicar las anotaciones de clase que serán marcadas como open

    apply plugin: "kotlin-allopen"
    
    allOpen {
        annotation("com.my.Annotation")
    }

Si estamos utilizando Spring, tenemos el plugin 

    apply plugin: "kotlin-spring"
    
Este plugin es una versión preconfigurada del plugin kotlin-allopen que automáticamente abre las clases anotadas con:

``@Component``, ``@Async``, ``@Transactional``, ``@Cacheable``, ``@Configuration``, ``@Controller``, ``@RestController``, ``@Service``, ``@Repository``

Podemos utilizar tanto kotlin-allopen y kotlin-spring en el mismo proyecto. El primero nos permite definir anotaciones personalizadas y el segundo ya trae las anotaciones de Spring requeridas para hacer las clases abiertas.

Otro plugin es:

    apply plugin: "kotlin"
    
Con el cual indicamos que el objetivo del proyecto es para la JVM, ya que también podemos hacerlo para android y para JavaScript.

Finalmente en las dependencias del proyecto podemos ver las siguientes:

	compile("org.jetbrains.kotlin:kotlin-stdlib-jre8:${kotlinVersion}")
	compile("org.jetbrains.kotlin:kotlin-reflect:${kotlinVersion}")
    
**kotlin-stdlib-jre8** Contiene las clases de la librería estándar de Kotlin para trabajar con java 8. Esto es lo que nos permite trabajar con kotlin. Si deseamos trabajar con Java 7 usariamos kotlin-stdlib-jre7 o simplemente kotlin-stdlib para versiones anteriores a Java 7.

**kotlin-reflect** Permite trabajar con Reflection para inspeccionar la estructura de nuestras aplicaciones en tiempo de ejecución.

## Kotlin y Spring

Ya tenemos todo para desarrollar un primer sencillo servicio REST. Para ello ocupamos una [clase data](https://kotlinlang.org/docs/reference/data-classes.html):

```kotlin
data class Student(val id:String, val name:String) {
}
```

No es el objetivo de este tutorial dar una explicación detallada sobre las características de Kotlin. La [referencia oficial](https://kotlinlang.org/docs/reference/basic-syntax.html) del lenguaje me parece muy buena y fácil de comprender.

Sin embargo mencionaré que las clases data son clases kotlin destinadas a mantener datos. Serían como clases value object o entities en Java con la ventaja que las clases data de Kotlin generan automáticamente los métodos

- equals() y hashCode()
- toString() de la forma "User(name=John, age=42)"
- copy() ver la referencia oficial

Ahora creamos el controlador REST de la siguiente manera:

```kotlin
@RestController
class StudentController {
    @GetMapping("/dummy-student")
    fun students(@RequestParam name:String):Student {
        val student = Student("1", name)
        return student

    }
}
```

Si nuestro método no hace mas que retornar un valor, podemos simplificar su escritura omitiendo el tipo de dato de regreso, las llaves del método y el return, de tal forma que tendríamos esto:

```kotlin
@RestController
class StudentController {
    @GetMapping("/dummy-student")
    fun students(@RequestParam name:String) = Student("1", name)
}
```

Como podemos ver, en Kotlin no existe la palabra *"new"* para crear una instancia de una clase.

Al ejecutar el proyecto con bootRun e ingresar desde nuestro navegador a [http://localhost:8080/dummy-student?name=Ascari](http://localhost:8080/dummy-student?name=Ascari) veremos el json resultante.

Para hacer el ejemplo un poco más interesante, nos conectaremos a una base de datos mongo usando Spring Data Mongo. Agregamos la dependencia:

	compile('org.springframework.boot:spring-boot-starter-data-mongodb-reactive')

A nuestra clase data, la modificamos para indicarle que se trata de un documento mongo y corresponderá con la colección en mongo llamada *"students"*

```kotlin
@Document(collection = "students")
data class Student(val id:String, val name:String) {
}
```

## Spring Data Mongo

Antes de empezar con el ejemplo, debes cargar a mongo algunos datos. Puedes obtener un data JSON desde este repositorio de [github](https://github.com/windoctor7/codigo-tutoriales-blog/raw/master/spring-webflux-2/src/main/resources/json/students.json) e importarlos a la base de datos **test** de mongodb.

    mongoimport -h 127.0.0.1:27017 --db test --collection students --file students.json
    
Usaremos Spring Data Mongo para conectarnos a una base de datos mongo, para ello creamos el siguiente repositorio

```kotlin
interface StudentRepository : CrudRepository<Student,String>{
    fun findByName(name:String): Flux<Student>
}
```

Lo anterior es una interfaz que extiende a la interfaz ``CrudRepository`` de Spring Data. El objetivo de los repositorios de Spring Data es reducir el código repetitivo que en casi todas las aplicaciones usamos. La interfaz principal de Spring Data es ``Repository`` de la cual hereda ``CrudRepository`` que utilizamos en este ejemplo.

``CrudRepository`` ofrece métodos CRUD predefinidos que podemos utilizar rápidamente. Lo mejor es que ``CrudRepository`` es solo una interfaz, existen múltiples implementaciones dependiendo de la tecnología de persistencia a utilizar y Spring Boot utilizará la implementación adecuada según existan ciertas librerías en el classpath de la aplicación. En este ejemplo, Spring Boot detectará que tenemos las dependencias de MongoDB y automáticamente se conectará al localhost con la base de datos **test**.

La interfaz ``CrudRepository`` tiene los siguientes métodos:

```java

// Permite guardar un documento mongo
<S extends T> S save(S entity);
                                     
// Busca un documento mongo por su ID
T findOne(ID primaryKey);
                                     
// Devuelve todos los documentos de la colección mongo
Iterable<T> findAll();

// Regresa el número de documentos que existen en la colección mongo
Long count();
                                                                    
// Elimina un documento mongo
void delete(T entity);

// Regresa true si existe un documento mongo con el ID indicado
boolean exists(ID primaryKey);
```

Lo importante es no perder de vista que se trata solo de una interfaz. ``CrudRepository`` no es exclusiva de Mongo, de hecho ni siquiera está en algún paquete que haga alusión a mongo. Si utilizamos una base de datos relacional, podemos usar Spring Data JPA y también podremos utilizar estos mismos métodos, Spring reconocerá automáticamente el tipo de tecnología de persistencia a utilizar.

Spring Data va más allá y permite definir métodos personalizados para realizar algún tipo de operación. Volviendo a nuestro repositorio de ejemplo:

```kotlin
interface StudentRepository : CrudRepository<Student,String>{
    fun findByName(name:String): Flux<Student>
}
```

Definimos el método ``findByName`` el cual buscará dentro de la colección **students** todos los documentos mongo que en su atributo "name" tengan el valor indicado por la variable. Sin escribir ningún tipo de código, solo un método, Spring Data nos devolverá una lista de valores!! Esta es la magina de Spring.

Finalmente modificamos nuestro controlador REST para dejarlo como sigue:

```kotlin
@RestController
class StudentController(val repository: StudentRepository) {

    @GetMapping("/dummy-student")
    fun students(@RequestParam name:String) = Student("1", name)

    @GetMapping("/students")
    fun studentByName(@RequestParam name:String) = repository.findByName(name)
}
```

Al ingresar a [http://localhost:8080/students?name=Bao%20Ziglar](http://localhost:8080/students?name=Bao%20Ziglar) podemos ver el resultado.

Kotlin es un gran y poderoso lenguaje que nos permite hacer muchas cosas de forma simple. En muy poco tiempo muchos frameworks han implementado soporte para Kotlin. Definitivamente Kotlin llego para quedarse y ahora que Google abrió el soporte oficial a Kotlin para el desarrollo en Android comenzará a ganar muchos más adeptos.

En próximos tutoriales escribiré más sobre este gran lenguaje. Déjame tus comentarios y no olvides descargar el código fuente.