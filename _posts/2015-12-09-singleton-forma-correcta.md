---
layout: post
title: Patrón Singleton, ¿synchcronize o no synchcronize?
tags:
- Patrones Diseño
- Java
- Singleton
---

El patrón de diseño Singleton nos permite crear una única instancia de una clase a los largo de todo el ciclo de vida de nuestra aplicación. En internet podemos encontrar algunas formas de implementar este patrón, por ejemplo en esta página de [Arquitectura Java](http://www.arquitecturajava.com/ejemplo-de-java-singleton-patrones-classloaders/) se muestra una implementación bastante mala ya que no es "thread safe", es decir, no es segura para concurrencia.

En dicha página tienen el siguiente código:

```java
    public  static Configurador getConfigurador(String url,String baseDatos) {

         if (miconfigurador==null) {
            miconfigurador=new Configurador(url,baseDatos);
         }
         return miconfigurador;
     }
```

Con lo anterior tenemos el problema que si 2 hilos acceden de forma concurrente al ``miconfigurador=new Configurador(url,baseDatos);``entonces ambos hilos crearán instancias de la clase Configurador. Por lo tanto no es una forma efectiva este diseño de singleton.

Una posible solución sería agregar un synchcronize al método 

```java
    public  static synchronized Configurador getConfigurador(String url,String baseDatos) {

         if (miconfigurador==null) {
            miconfigurador=new Configurador(url,baseDatos);
         }
         return miconfigurador;
     }
```
  
  de esta forma conseguiriamos una clase "thread safe" ya que al ingresar un hilo al método se conseguirá una zona de exclusión mutua, es decir, cuando un segundo hilo intente acceder al método no podrá puesto que ya hay un hilo adentro. De esta forma aseguramos tener una sola instancia en ambientes concurrentes. Sin embargo, esta solución también presenta un problema y es el "performance".
  
  * Cuando el hilo A acceda al método, el método estará bloqueado.
  * Inmediatamente un hilo B quiere acceder al método pero tendrá que esperar a que el hilo A salga.
  * En cuestión de milisegundos podríamos tener a los hilos C,D,F,G.....Z esperando a que los demás hilos salgan del método.
  
  Como es evidente tenemos un problema de performance pues al quedar bloqueado el método, los demás hilos deben esperar.
  
  Podríamos encontrar una solución al problema anterior haciendo un [double-checked locking]("https://en.wikipedia.org/wiki/Double-checked_locking#Usage_in_Java) el cuál se considera un Anti patrón y consiste en hacer una doble comprobación:
  
  ```java
      public  Configurador getConfigurador(String url,String baseDatos) {

        if (miconfigurador==null) { //se comprueba 1 vez
            synchronized (this){
                if (miconfigurador==null) { //se comprueba 2 veces
                    miconfigurador=new Configurador(url,baseDatos);
                }
            }
        }
        return miconfigurador;
    }
```

Sin embargo como describe la wikipedia, también tenemos algunos inconvenientes con esto.

Finalmente la mejor solución es crear la instancia "inline", es decir, en la misma linea en la que se declara,
`` private static final Configurador instance = new Configurador();``

De esta forma la instancia se creará en el momento en el que la JVM cargue la clase, que es incluso antes que esté disponible para cualquier hilo.

Pueden ver un ejemplo completo de su implementación [aquí](https://github.com/iluwatar/java-design-patterns/blob/master/singleton/src/main/java/com/iluwatar/singleton/IvoryTower.java)

Finalmente en el libro [Effective Java](http://www.amazon.com/Effective-Java-Edition-Joshua-Bloch/dp/0321356683) se menciona que la forma más limpia y sencilla de crear un singleton en Java solo a partir de la versión 5 es mediante enums.

```java
    public enum MiSingleton {
        INSTANCE;

        public void miMetodo() { aqui nuestro codigo }
    }
    
    //La forma de llamarlo sería
    MiSingleton.INSTANCE.miMetodo();
```

