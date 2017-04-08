---
layout: post
title: Desarrollo web ágil en Java con Tapestry
tags:
- Tapestry
- Java
- Gradle
- IntelliJ
---

![Logo Tapestry](http://tapestry.apache.org/images/tapestry.png) 
[Tapestry](http://tapestry.apache.org/) es un Framework orientado a componentes para la creación de aplicaciones web altamente escalables. Entre sus características destacan:

* Poliglota, esto es que se puede desarrollar escribiendo código en Java, Groovy o Scala! 
* Muy productivo al tener Live Class Reloading, es decir que no tienes que reiniciar el servidor para ver los cambios realizados a la mayoría de tus componentes.
* Testeable debido a que a diferencia de otros Frameworks como [Struts](https://struts.apache.org/) nuestras clases no heredan de otras clases extrañas que complican la creación de pruebas unitarias.
* Sigue la idea de convención sobre configuración.
* Utiliza su propio motor de inyección de dependencias, [Tapestry IoC](https://tapestry.apache.org/ioc.html)
* Utiliza [BootStrap 3](http://getbootstrap.com/) y [JQuery](https://jquery.com/)

Al momento de escribir este articulo la última versión estable liberada hace unos días es la 5.4.0


### Creando el proyecto
Tapestry tiene una rápida forma de crear un proyecto a partir de una estructura predeterminada mediante un arquetipo de Maven, además que nos viene con un pequeño ejemplo para verlo en acción de inmediato. Si usas Gradle como yo, no importa, al crear el arquetipo también nos generá un archivo build.gradle.

Lo único que tenemos que hacer es tener Maven y abrir nuestra consola en el directorio donde deseamos crear el proyecto y ejecutar la siguiente línea:

``mvn archetype:generate -DarchetypeCatalog=http://tapestry.apache.org``

Al ejecutar el comando anterior, en la consola tendremos una salida con la lista de posibles arquetipos. Buscaremos el que diga Tapestry QuickStart. La salida que yo tengo es la siguiente:

    Choose archetype:
    1: http://tapestry.apache.org -> org.apache.tapestry:quickstart (Tapestry 5 Quickstart Project)
    2: http://tapestry.apache.org -> org.apache.tapestry:tapestry-archetype (Tapestry 4.1.6 Archetype)
    Choose a number or apply filter (format: [groupId:]artifactId, case sensitive contains): : ___aqui teclear el 1____

Al momento de escribir este tutorial es la opción 1 la que nos permitirá crear un proyecto con la versión 5 de Tapestry. 

Después nos preguntará que versión en específico de tapestry que queremos usar para crear el proyecto, en mi caso elegí la última versión al momento de escribir este tutorial que es la versión 5.4.0

    Choose org.apache.tapestry:quickstart version: 
    1: 5.0.19
    2: 5.1.0.5
    3: 5.2.6
    4: 5.3.7
    5: 5.4.0
    Choose a number: 5: ___TECLEAR 5____


Este QuickStart se encuentra descrito en el siguiente [link](http://tapestry.apache.org/getting-started.html) que te recomiendo leer en caso que tengas algún problema hasta el momento.

Después de elegir la versión te preguntará por el groupId y artifactId.

Listo!!! Si nunca has usado Maven seguramente lo anterior te parece mucho rollo y bueno, te recomiendo dedicarle unos 15 minutos a leer algún tutorial de Maven, en cuanto lo termines, regresa aquí, no te desanimes!!

Si ya usas Maven o Gradle, en hora buena!! Ahora importa el proyecto MAVEN desde tu IDE favorito, que en mi caso es [IntelliJ IDEA](https://www.jetbrains.com/idea/).

De nueva cuenta Listo!!! Toda vez que importaste el proyecto y se descargaron las dependencias, para correr el proyecto simplemente ejecuta la tarea ``jetty:run`` y tendrás un proyecto de ejemplo ya funcionando...

![Proyecto ejecutandose](https://windoctor.github.io/static/img/tapestry-getting-started.png)

Hasta aquí, realizar todo lo anterior no lleva más de 3 minutos!! En solo 3 minutos ya tenemos un proyecto de ejemplo corriendo, basta abrir nuestro navegador.

La estructura de carpetas de nuestro proyecto luce algo así:

![Estructura proyecto tapestry](https://windoctor.github.io/static/img/estructura_tapestry.png)

Vamos a crear un sencillo formulario que me permita agregar nuevos clientes, así que primero creamos nuestro POJO

{% highlight java %}

    package mx.com.coffeedev.tapestry.entities;

    import mx.com.coffeedev.tapestry.data.Genero;

    public class Cliente {

        private String nombre;
        private String apellidoPaterno;
        private String apellidoMaterno;
        private Genero sexo;

    }

    //Crear también los getters y setters con ayuda del IDE

{% endhighlight %}


Como observamos en el código anterior, tenemos una referencia a una clase llamada Genero, la cuál es un Enum, así que la creamos.

{% highlight java %}
    package mx.com.coffeedev.tapestry.data;

    public enum Genero {
        MASCULINO, FEMENINO
    }
{% endhighlight %}

Ahora en la clase Index que se encuentra en el paquete "pages" vamos a agregar un nuevo atributo de clase,

  ``@Property``
  ``private Cliente cliente;``
  
La anotación @Property le indica a Tapestry que se trata de una propiedad por lo tanto en tiempo de ejecución Tapestry creará los setters/getters para el atributo cliente.

Finalmente en el archivo Index.tml (se encuentra en el paquete "pages" pero de la carpeta resources) vamos a agregar la siguiente linea al final de todo (pero antes de la etiqueta </html> obviamente)

``<t:beaneditform object="cliente"/>``

Les comenté que Tapestry tiene Live Class Reloading por lo que si estás usando Eclipse como IDE y tienes habilitado el compilado automático, sin necesidad de reiniciar la aplicación, ve al navegador y refresca la página, al final podrás ver que se nos ha creado un formulario mágicamente!!

![Formulario](https://windoctor.github.io/static/img/formulario_wizard.png)

Pero Tapestry tiene muchisimas cosas más que hacen el desarrollo ágil, como por ejemplo sus validadores! Basta con ir a nuestra clase Cliente.java y agregar la siguiente anotación al atributo nombre.


``@Validate("required")``
``private String nombre;``

En este punto si necesitamos reiniciar nuestro servidor Jetty debido a que el Live Class Realoading solo aplica a componentes y páginas, no así a beans o clases de servicio, pero el reinicio es rapidísimo. Ahora al querer envíar nuestro formulario obtendremos un error

![Formulario validado](https://windoctor.github.io/static/img/form_validate.png)

Y bueno, como podemos ver con Tapestry podemos crear atractivas interfaces de usuario mágicamente, ideal para prototipos ó formularios productivos, de cualquier forma si no queremos usar el componente ``beaneditform``podemos crear los formularios manualmente.

En próximos tutoriales hablaré más acerca de este buen Framework.