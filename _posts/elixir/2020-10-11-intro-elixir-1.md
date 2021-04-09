---
layout: post
title: Introducción a Elixir - Resolviendo kata de codewars.com
category: elixir
overview: Solucionamos la kata "Two Sum" de codewars para explicar las diferencias entre Java y Elixir. 
image: card_elixir_java.png
tags: ['elixir']
---

# Introducción
Elixir es un lenguaje de programación funcional y es ideal para quebrar la cabeza a quienes venimos de lenguajes orientados a objetos como Java, por ejemplo no tenemos sentencias de repetición como los bucles ``for/while``. Aquellos problemas que requieran utilizar estructuras de repetición tradicionales, deberán resolverse  con recursividad.
 
Lo anterior definitivamente te quebrará la cabeza. Si es tu primer acercamiento con un lenguaje funcional, te parecerá increible que no existan este tipo de estructuras, pero como verás, no son necesarias.

Para empezar, debemos instalar elixir, lo cual resulta muy sencillo, en Mac usando Homebrew, en Linux con el gestor de paquetes o en Windows bajando el instalador. Revisa las instrucciones de instalación [aquí](https://elixir-lang.org/install.html)

```zsh
❯ brew install elixir
...
❯ elixir -v
Erlang/OTP 23 [erts-11.1.5] [source] [64-bit] [smp:4:4] [ds:4:4:10] [async-threads:1] [hipe] [dtrace]

Elixir 1.11.3 (compiled with Erlang/OTP 23)
```
Cabe mencionar que junto con elixir, también se bajará la máquina virtual de Erlang debido a que corre sobre ella. Valida que elixir se haya instalado correctamente tecleando ``elixir -v``.

Antes que nada, empecemos por crear un proyecto usando la herramienta MIX de elixir y posteriormente editar el archivo ``kata.ex`` que se encuentra en ``kata/lib``

```elixir
❯ mix new kata
❯ cd kata
❯ vim lib/kata.ex
```

Vamos a sustituir el contenido del archivo por este otro:

```elixir
defmodule Kata do
   def hi() do
      IO.puts("Hi ¿Cómo estás?")
   end
end
```

Finalmente ingresamos al modo interactivo de elixir con ``iex`` y ejecutamos nuestra función ``hi()``

```elixir
❯ iex -S mix
iex(1)> Kata.hi()
Hi ¿Como estas?
:ok
```

Estamos listos para pasar a resolver el problema!

---

# El Problema

El reto es una kata de [codewars.com](https://www.codewars.com/kata/52c31f8e6605bcc646000082/train/elixir) y consiste en que dado un arreglo de enteros, buscar 2 números diferentes que sumados me den el valor de un segundo parámetro llamado **target**. Por ejemplo:

```elixir
#¿Cuáles son los 2 números que sumados nos da 4?
([1,2,3], 4)
```
En este caso, es claro que los números son 1 y 3, por lo tanto el programa debe devolver el índice de su posición en el arreglo, es decir, 0 y 2.

```
two_sum([1, 2, 3], 4) == {0, 2}
```

Una implementación simple es tomar el primer elemento del arreglo y sumarlo con cada uno de los elementos posteriores y validar si la suma es 4, de lo contrario tomar el segundo elemento del arreglo y sumarlo con el resto de los elementos posteriores y así repetidamente.

![algoritmo](static/img/kata_elixir_algoritmo.png)

Entendido el problema, veamos una implementación básica en Java usando únicamente arreglos.

```java
    public static int[] twoSum(int[] numbers, int target)
    {
       for(int i = 0; i < numbers.length; i++) {
           for(int j = i + 1; j < numbers.length; j++) {
               if(numbers[i] + numbers[j] == target){
                   return new int[]{i, j};
               }
           }
       }
      return null; 
    }
```

Con la implementación anterior seguro que estamos tentados a hacer algo similar en Elixir, sin embargo, los lenguajes de programación funcional **carecen de bucles** y no guardan estados en sus variables, es decir, son inmutables. JavaScript no es un lenguaje que se pueda considerar funcional, aunque al igual que muchos otros como Java, Ruby o Kotlin que son Orientados a Objetos, permiten hacer programación funcional. Te lo repito de nuevo, en elixir **¡No hay bucles!, no hay ciclos for o while!!** y no hace falta ya que la solución a esto es la **¡Recursividad!**.


Sin embargo, para esta kata usaremos el módulo ``Enum`` que nos provee de varias funciones para iterar listas. Así pues, la solución en elixir a nuestro problema es la siguiente:


```elixir
  def two_sum(numbers, target) do
    numbers|> Enum.with_index
    |> Enum.map(fn {x, i} -> {Enum.find_index(numbers, &(&1 + x == target)), i} end)
    |> Enum.filter(fn {a, b} -> a != nil and a != b end)
    |> hd
  end
```

Definimos una función llamada ``two_sum`` que recibe dos parámetros y como puedes ver, a diferencia de Java,  en elixir no se declara el tipo de dato que van a contener las variables. 

Agrega la funcion ``two_sum()`` al archivo ``Kata.ex`` que venimos manejando y entonces entra al modo interactivo de elixir para ejecutarlo

```elixir
❯ iex -S mix
iex(1)> Kata.two_sum([1,2,3],4)
{2, 0}
```

Como puedes ver, nos devuelve la posición 2 y 0 del arreglo, que es dónde se encuentran los números 3 y 1 que al sumarlos nos da 4.

Expliquemos ahora un poco sobre la sintaxis de elixir.

## Operador Pipe

Del código anterior también desprendemos el uso de algo extraño llamado **operador pipe** ``|>`` el cuál nos ayuda a simplificar y mantener legible la llamada a múltiples funciones. En otros lenguajes de programación podemos tener algo como esto:


```elixir
funcion1(funcion2(funcion3(funcion4(funcion5()))))
```

¿Engorroso no crees?... Además, la lectura es anti-natural, ya que debemos leer de derecha a izquierda, es decir, primero se ejecuta la funcion5 y el resultado se le pasa a la funcion4 y así sucesivamente.

En elixir, algo más elegante es usar el operador pipe 

```elixir
funcion5() |> funcion4() |> funcion3() |> funcion2() |> funcion1()
```

Observa que con este operador, el resultado se lee y ejecuta de izquierda a derecha. Primero se ejecuta ``funcion5()`` y el resultado  que devuelve es pasado como parámetro de entrada a la ``funcion4()`` que a su vez devuelve un resultado que es pasado a ``funcion3()`` y asi sucesivamente,  definitivamente algo  más natural al momento de leer el código.

## Módulo Enum
En elixir, un módulo podemos compararlo con lo que es una clase en Java. Al igual que una clase tiene métodos, un módulo tiene funciones.

Entonces, de nuestro código en elixir, tenemos que el arreglo ``numbers`` es pasado como parámetro de entrada a la función ``with_index()`` del módulo ``Enum``. El módulo ``Enum`` incluye más de 70 funciones para trabajar con colecciones. 

La función ``with_index()`` recibe un ``Enumerable`` como por ejemplo una lista y devuelve otra lista con los índices que tiene cada elemento en la lista original

```elixir
Enum.with_index([1,2,3,4])
>    [{1, 0}, {2, 1}, {3, 2}] 
``` 

Si para este punto ya te diste cuenta que de pronto utilizo la palabra arreglo y lista de manera indistinta, es porque en elixir es lo mismo, a diferencia de Java a donde llamamos lista a todo aquel objeto de la clase ``List`` y arreglo a todo aquel que es declarado con corchetes ``[ ]`` (no entraré en detalles sobre como funcionan en memoria las distintas colecciones en elixir a diferencia de Java).

Continuando con la explicación, la lista devuelta por la función ``with_index`` será el parámetro de entrada a la función ``map()`` la cual recibe dos parámetros de entrada, un Enumerable y una función. El funcionamiento de map es similar al ``map`` que tienen los Streams en Java o en otros lenguajes por lo tanto no me detendré a explicarlo. Finalmente map devuelve otra lista con el resultado de la función aplicada. La signatura de la función map es la siguiente:

```elixir
map(enumerable, fun) :: list
```

``map`` recibe dos parámetros, el primero se pasa "implicitamente" mediante el operador pipe, de tal forma que solo debemos especificar el segundo parámetro que es la función a aplicar. Básicamente esto es lo que tenemos:

---

![map elixir](/static/img/map_elixir3.png)

---

## Funciones anónimas
Para definir una función anónima en Elixir necesitamos las palabras clave ``fn`` y ``end``.

```elixir
sum = fn (a, b) -> a + b end
sum.(2, 3)
```

Sin embargo, existe una manera aún más anónima, usar un atajo mediante el operador ampersand ``&``

```elixir
sum = &(&1 + &2)
sum.(2, 3)
```
  en dónde ``&1``y ``&2`` corresponden a nuestras variables

Bueno, ya me canse de escribir y como desde el inicio mi intención no fue escribir un tutorial, si no más bien una nota informativa, creo que hasta aquí le dejo.

Pero para estar en las  mismas condiciones, usemos programación funcional que Java incorporó a partir de la versión 8 y resolvamos el problema.

```java
    public static int[] twoSum2(int[] numbers, int target) {
        for (int i = 0; i < numbers.length; i++) {
            int j = target - numbers[i];
            if (Arrays.stream(numbers).anyMatch(x -> x == j)) {
                List<Integer> li = Arrays.stream(numbers).boxed().collect(Collectors.toList());
                return new int[]{i, li.indexOf(target - numbers[i])};
            }
        }
        return null;
    }
```

Ahora si, se ve un poco más pareja la situación.

## REFERENCIAS
https://elixirschool.com/es/lessons/basics

https://hexdocs.pm/elixir


