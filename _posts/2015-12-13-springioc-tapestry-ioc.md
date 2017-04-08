---
layout: post
title: Spring IoC y Tapestry IoC en modo standalone
tags:
- Tapestry
- Spring
- Inyección Dependencias
---

Spring es uno de los frameworks más conocidos por su contenedor de inversión de control que permite llevar a cabo la [inyección de dependencias](https://es.wikipedia.org/wiki/Inyecci%C3%B3n_de_dependencias), aunque Spring es es más que eso, es todo un Framework robusto con muchos proyectos a su alrededor como por ejemplo:

* <kbd>Spring MVC</kbd> para la construcción de aplicaciones web siguiendo el patrón Modelo Vista Controlador.
* <kbd>Spring Data</kbd> para el acceso a Bases de Datos relacionales y NoSQL.
* <kbd>Spring Security</kbd> que proporciona un modelo de autorización y autenticación a nuestras aplicaciones.
* <kbd>Spring Social</kbd> para la integración de redes sociales a nuestra aplicación
* <kbd>Spring Android</kbd> para utilizar componentes clave de Spring en el desarrollo de apps android.
* Un largo etcetera...

Por su parte, [Tapestry](http://tapestry.apache.org/) es un potente Framework para el desarrollo ágil, limpio y escalable de aplicaciones web, además de permitir que las pruebas unitarias sean realmente fáciles de implementar. A partir de la versión 5, Tapestry se modularizó para tener su propio [contenedor IoC](https://tapestry.apache.org/ioc.html) independiente del nucleo de Tapestry, de tal forma que pueda ser usado de forma standalone, es decir, en cualquier aplicación no web.

Este tutorial cubre la inyección de dependencias usando Tapestry IoC y Spring IoC.

## Inyectando Dependencias con Tapestry

* Con nuestro IDE favorito creamos un proyecto Gradle.
* Agregamos la dependencia de Tapestry IoC que al día de hoy es la 5.4 RC1.

`` compile 'org.apache.tapestry:tapestry-ioc:5.4-rc-1 ``

* Creamos una intefaz y su implementación

<script src="https://gist.github.com/windoctor/8e2c24488043573f2914.js"></script>
<script src="https://gist.github.com/windoctor/ebfe47ea936a34a88e25.js"></script>

* Creamos la clase MessagePrinter encargada de imprimir en Consola el mensaje. A esta clase es a la que se le inyectará un objeto IMessage.

<script src="https://gist.github.com/windoctor/46a024d9d14dc458d275.js"></script>

* Para que tapestry pueda hacer la inyección de dependencias es necesario definir en una clase las implementaciones. Tapestry sigue el principio de convención sobre configuración por lo tanto el método debe llamarse "bind".

<script src="https://gist.github.com/windoctor/866112572cdea2078e0d.js"></script>

* Finalmente creamos una clase con método main e inicializamos el registro de tapestry, algo así como lo que en Spring sería el ApplicationContext.

<script src="https://gist.github.com/windoctor/e806b09bf9becf6fc004.js"></script>

## Inyectando Dependencias con Spring

Tomando las mismas clases del ejemplo con Tapestry tenemos que marcar con la anotación ``@Component`` a las clases que serán inyectadas,  en este caso es la clase Message a la que debemos marcar como un componente de Spring para poder ser inyectada desde MessagePrinter.

{% highlight java %}
    @Component
    public class Message implements IMessage {
        @Override
        public String getMessage() {
            return "Mi primer ejemplo con Tapestry IOC!!!";
        }
    }
{% endhighlight %}

A la clase MessagePrinter le agregaremos la anotación de Spring llamada ``@Autowired`` además que también le agregaremos la anotación @Component. Notar que le dejamos la anotación @Inject de Tapestry. Se la podemos quitar pero para tener nuestros dos ejemplos funcionando se la dejaremos.

{% highlight java %}
    @Component
    public class MessagePrinter {
    
        private final IMessage message;

        @Autowired
        @Inject
        public MessagePrinter(IMessage message) {
            this.message = message;
        }

        public void printMessage(){
            System.out.println(message.getMessage());
        }

    }
{% endhighlight %}


Finalmente vamos a crear otra clase con método main que contendrá el ApplicationContext de Spring 

{% highlight java %}
    @ComponentScan
    public class ApplicationSpring {

        public static void main(String[] args) {
            ApplicationContext context = 
               new AnnotationConfigApplicationContext(ApplicationSpring.class);
            MessagePrinter messagePrinter = context.getBean(MessagePrinter.class);
            messagePrinter.printMessage();
        }
    }
{% endhighlight %}