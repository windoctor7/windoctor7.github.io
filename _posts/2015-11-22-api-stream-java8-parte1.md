---
layout: post
title: Introducción al API Stream de Java 
category: java
source: https://gist.github.com/windoctor/650f566bfcc3617f0913
overview: Una breve introducción al API Stream de Java 8
tags:
- API Stream
- Java 8
- Collections
---


A partir de la versión Java 8 se incluye un interesante API para realizar filtros y consultas a una colección, por ejemplo a un ArrayList.

Primero que nada veamos el uso de lambdas para iterar e imprimir una lista de objetos de tipo Cancion (no pierdas de vista la lista canciones ya que la ocuparemos a lo largo del tutorial)

```java
	List<Cancion> canciones = new ArrayList<>(Arrays.asList(
		new Cancion("Te Equivocas", "Jon Carlo"),
		new Cancion("Solo tu", "Orlando Elizalde"),
		new Cancion("La Mano de Dios","Jon Carlo"),
		new Cancion("Como una hoja de papel", "Evelyn Vasquez"),
		new Cancion("Siempre te amare", "Darwin Lechler"),
		new Cancion("Brilla", "Darwin Lechler"),
		new Cancion("Sin amor", "Carlos & Carito"),
		new Cancion("Te amare", "Joan Sánchez"),
		new Cancion("Cuanto me ama", "Orlando Elizalde")
	));
		
	//Anterior a java 8
	for(Cancion cancion : canciones){
		System.out.println("Java 7: "+cancion)
	}
	
	//Uso de lambdas en Java 8
	canciones.forEach( c -> System.out.println("Java 8: "+c));
```

<p class="codigo">
Listado 1
</p>


El método [forEach](https://docs.oracle.com/javase/8/docs/api/java/lang/Iterable.html#forEach-java.util.function.Consumer-) pertenece a la interfaz [Iterable](https://docs.oracle.com/javase/8/docs/api/java/lang/Iterable.html) y es el que muestra el uso de lambdas, "c" es la referencia al item actual de la lista (es decir, la canción actual) y con "->" indicamos la acción a ejecutar. Entonces simplemente estamos diciendo que deseamos imprimir en consola cada item que contiene la lista.



### Filtrando elementos


Si de la lista anterior deseamos obtener todas las canciones cuyo cantante sea "Jon Carlo", en versiones anteriores a Java 8 tendríamos que iterar cada elemento comparando el nombre del cantante y de ser el que buscamos lo agregaríamos a otra lista para devolverla al final.


```java
for(Cancion cancion : filtrarCantanteJava7(canciones, "Jon Carlo")){
	System.out.println("Java 7: "+cancion);
}
							
public List<Cancion> filtrarCantanteJava7(List<Cancion> canciones, String cantante){
	List<Cancion> listaFiltrada = new LinkedList<>();
	for(Cancion cancion : canciones){
		if(cancion.getCantante().equals(cantante))
			listaFiltrada.add(cancion);
	}
	return listaFiltrada;
}	
```

<p class="codigo">
Listado 2
</p>

A partir de Java 8, simplemente tendríamos que obtener un objeto [Stream](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html), aplicar un filtro mediante el método "*filter*" y finalmente cerrar el proceso devolviendo el resultado en un List mediante `.collect(Collectors.toList())`


```java
List<Cancion> listadoCanciones = filtrarCantanteJava8(canciones,"Jon Carlo");
listadoCanciones.forEach(c -> System.out.println("Java 8: "+c));

public List<Cancion> filtrarCantanteJava8(List<Cancion> canciones, String cantante){
	return canciones.stream()
	                .filter(c -> c.getCantante().equals(cantante))
	                .collect(Collectors.toList());
}
```
<p class="codigo">
Listado 3
</p>


### Método map

Una vez aplicado el filtro, quizá queremos obtener el nombre de los cantantes en mayúscula! Invocando al método map, nuestro código ahora queda de esta forma:


```java
public List<Cancion> filtrarCantanteJava8(List<Cancion> canciones, 												String cantante){
	return canciones.stream()
	.filter(c -> c.getCantante().equals(cantante))
	.map( c -> {
			c.setCantante(c.getCantante().toUpperCase());
			return c;
		}
	)
	.collect(Collectors.toList());
} 
```

<p class="codigo">
Listado 4
</p>

En el método map podemos mapear el resultado según nuestra conveniencia. El método map retorna siempre un objeto del mismo tipo del Stream, en este caso, el tipo del Stream es Cancion y por ello es necesario agregar el `return c`

¿Y si solo queremos recuperar el nombre de las canciones en lugar de todo el objeto Canción? Fácil...


```java
public List<String> obtenerCancionesPorCantanteJava8(List<Cancion> canciones, String cantante){
	return canciones.stream()
		.filter(c -> c.getCantante().equals(cantante))
		.map(c -> c.getNombre().toUpperCase())
		.collect(Collectors.toList());
}
```

<p class="codigo">
Listado 5
</p>

Aquí el tipo del Stream es un String y no es necesario agregar un `return`como en el listado 4 debido a que el mismo método `toUpperCase()` devuelve un String.

### Quitando duplicados
Mediante el método distinct podemos quitar elementos duplicados de nuestra lista. Vamos a agregar una canción que ya existe en la lista de canciones y después aplicaremos el método distinct

```java
//Esta canción ya existe pero la agregaremos nuevamente
canciones.add(new Cancion("Siempre te amare", "Darwin Lechler"));
// aplicamos el distinct
canciones.stream().distinct().collect(Collectors.toList()).forEach( System.out::println);
```
<p class="codigo">
Listado 6
</p>

Al ejecutar el código anterior, podremos ver que efectivamente evitamos elementos duplicados, aunque claro, para que lo anterior funcione, debemos sobreescribir los métodos equals y hashCode en la clase Cancion. Para evitarnos el engorroso trabajo, hay que generar estos métodos automáticamente con el IDE que estemos usando, Eclipse, NetBeans ó en mi caso en IntelliJ IDEA basta con situarse en la clase Cancion, dar click derecho y seleccionar "Generate..." para finalmente seleccionar "equals and hashCode". 

### Contando elementos
¿Y si queremos saber cuántas canciones tiene un determinado cantante? Muy fácil, aplicamos el filtro y después invocamos al método count.

```java
long count = canciones.stream()
	.filter(c -> c.getCantante().equalsIgnoreCase("Jon Carlo"))
	.count();
System.out.println("Jon Carlo aparece: "+count+" veces");
```
<p class="codigo">
Listado 7
</p>

### Agrupando canciones por Cantante
Como seguro te has dado cuenta, el concepto es como las consultas SQL y para que tenga aún más similitud, vamos a agrupar el número de canciones por cantante, es decir, vamos a saber cuántas canciones tiene cada cantante

```java
Map<String, Long> counted = canciones.stream().collect(
	Collectors.groupingBy( 
		c -> c.getCantante(), Collectors.counting()
	)
);
System.out.println(counted);
```
<p class="codigo">
Listado 8
</p>

Lo que nos devolverá en la salida
`{Joan Sanchez=1, Orlando Elizalde=2, Carlos & Carito=1, JON CARLO=2, Evelyn Vasquez=1, Darwin Lechler=3}`

Para ver más operaciones de reducción que podemos implementar como la del agrupado, revisen la documentación de la clase [Collectors](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html)
