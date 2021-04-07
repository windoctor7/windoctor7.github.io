---
layout: post
section-type: post
title: Tu primer skill alexa con Java
category: alexa-skills
overview: Desarrollaremos una skill que sume dos numeros. El backend estará construido en Java y lo subiremos a Amazon Lambda
tags: ['alexa-skills' ]
image: card_alexa.png
source: https://github.com/windoctor7/codigo-tutoriales-blog/tree/master/calculadora
---

El día de hoy vamos a aprender como desarrollar una primer skill para Amazon Alexa. Actualmente estoy desarrollando una skill para publicar y recibir notificaciones en Twitter vía Alexa a la que llamaré **TuitLexa** o simplemente **Mis Tuits**, recientemente publiqué un video con el pequeño avance que llevo. El código fuente de esta skill será de código abierto y lo publicaré en su momento en mi repositorio github.

El único requisito para seguir este tutorial es saber Java y Gradle (o Maven). Para desarrollar una buena skill, necesitamos no solamente teoría de programación, también requerimos conocer sobre los 4 principios del diseño situacional que son: **be adaptable, be personal, be available y be relatable**, principios que nos saltaremos por el momento dado que seguramente muchos de ustedes estarán impacientes por comenzar con su primer skill, así que omitiré muchas cosas teóricas que dejaré para una segunda parte. Por lo tanto, si hay algo que no te queda muy claro, no te preocupes, en una segunda parte explicaré a mayor detalle lo que estaremos haciendo aquí.


Antes que nada necesitamos una [cuenta en AWS](http://console.aws.amazon.com/), así que registrarse para obtenerla y después continuar con el tutorial.

# Crear la skill

Nuestra primer skill será muy sencilla pero suficiente para mostrar los conceptos de **Utterance, Intents y Slots**. La skill nos permitirá decirle a Alexa que sume dos números, una conversación podría ser la siguiente:

```properties
    Usuario: Alexa, abre dime la suma
    Alexa: Hola raton con cola, yo puedo sumar dos números, dime cuales
    Usuario: Suma cinco mas diez
    Alexa: cinco mas diez es igual a quince
```

Definido esto, manos a la obra. Para crear la skill debes dirigirte a la [consola de desarrollo](https://developer.amazon.com/alexa/console/ask) de Alexa, la cual tiene un aspecto como la siguiente imagen:

  ![Consola](https://raw.githubusercontent.com/windoctor7/windoctor7.github.io/master/static/img/consola-alexa.png)


Da clic en el botón **Create skill** y establece los siguientes valores:

- **Skill name:** calculadora
- **Default language:** Spanish (ES)
- **Choose a model to add:** Custom
- **Choose a method to host:** Provision your own

La opción **Provision your own** nos permitirá ejecutar los recursos backend en la plataforma **Amazon Lambda.**

Una vez creada la skill, veremos una pantalla donde del lado derecho existirá una especie de checklist con 4 pasos escenciales que debemos cubrir para crear nuestra skill, vayamos en órden y demos click sobre la opción **Invocation Name**

  ![Consola](https://raw.githubusercontent.com/windoctor7/windoctor7.github.io/master/static/img/check-list-alexa.png)



## 1. Definiendo el Invocation Name

En esta parte debes establecer algo conocido como el Invocation Name, el cual es el nombre por el que le dirás a Alexa que abra tu skill, según nuestro ejemplo de conversación de arriba, el Invocation Name es "dime la suma". Para crear el Invocation Name sigue estos 3 pasos:

1. Define el nombre
2. Guarda el modelo
3. Regresa a la vista de build

  ![alexa](https://raw.githubusercontent.com/windoctor7/windoctor7.github.io/master/static/img/invocation-name.png)

Ahora podrás ver que el punto de Invocation Name está completo y marcado con una palomita verde.

  ![alexa](https://raw.githubusercontent.com/windoctor7/windoctor7.github.io/master/static/img/invocation-name-complete.png)

El próximo paso ahora es establecer los Intents, Utterances y Slots que necesitaremos, así que da clic en **Intents, Samples, and Slots >**

## 2. Intents, Utterances y Slots

Los Intents (intenciones) son las acciones que el usuario pedirá a Alexa. Los Intents no son mas que un nombre mediante el cuál en nuestro backend referenciaremos para hacer algo, en este caso en el código Java usaremos este Intent para realizar la suma. Por el momento sigue estos 3 pasos

1. Establece el nombre (importante sea tal cual la imagen ya que así está referenciado en el código Java)
2. Crea el Intent
3. Guarda el modelo

  ![alexa](https://raw.githubusercontent.com/windoctor7/windoctor7.github.io/master/static/img/create-intent.png)

Al crear el Intent, pasarás a otra pantalla para crear los **Utterances** o enunciados. Los Utterances son las frases que el usuario puede decirle a Alexa para que nos sume dos numeros. En nuestra conversación de ejemplo que vimos al inicio, el Utterance es

    Usuario: Suma cinco mas diez   

Sin embargo, el usuario puede decir variantes como por ejemplo:

    suma cinco y diez
    cuanto es cinco mas diez
    cinco mas diez
    sumale cinco a diez

De lo anterior desprendemos que tenemos dos variables, a estas variables son a lo que denominados **Slots**.

    Suma {uno} mas {dos}

Por lo tanto **{uno} y {dos}** son nuestros slots. Primero vamos a crear nuestros Slots, en la pantalla actual localiza en la parte de abajo el apartado **Intent Slots** y agrega dos Slots de tipo ``AMAZON.NUMBER``, la siguiente imagen describe lo que debes hacer:

  ![alexa](https://raw.githubusercontent.com/windoctor7/windoctor7.github.io/master/static/img/intent-slots.png)


Ahora en la misma pantalla en la parte de arriba es donde debemos crear los Utterances, es decir, las frases que creemos que el usuario puede decirle a Alexa para que sume dos números. Completa los siguientes pasos y que se pueden observar en la imagen:

1. Agrega las Utterances en combinación con los Slots
2. Guarda el modelo
3. Compila el modelo
4. Es hora de ir a ver que el Intent, los Utterances y Slots se agregan a un archivo JSON

  ![alexa](https://raw.githubusercontent.com/windoctor7/windoctor7.github.io/master/static/img/utterances-slots.png)

El JSON contiene el nombre de nuestro Intent creado y que se llama "SumaIntent", así mismo están los Utterances y Slots.

```json
{
    "interactionModel": {
        "languageModel": {
            "invocationName": "dime la suma",
            "intents": [
                {
                    "name": "AMAZON.CancelIntent",
                    "samples": []
                },
                {
                    "name": "AMAZON.HelpIntent",
                    "samples": []
                },
                {
                    "name": "AMAZON.StopIntent",
                    "samples": []
                },
                {
                    "name": "AMAZON.NavigateHomeIntent",
                    "samples": []
                },
                {
                    "name": "SumaIntent",
                    "slots": [
                        {
                            "name": "uno",
                            "type": "AMAZON.NUMBER"
                        },
                        {
                            "name": "dos",
                            "type": "AMAZON.NUMBER"
                        }
                    ],
                    "samples": [
                        "{uno} mas {dos}",
                        "cuanto es {uno} mas {dos}",
                        "{uno} y {dos}",
                        "suma  {uno} y {dos}",
                        "suma {uno} mas {dos}"
                    ]
                }
            ],
            "types": []
        }
    }
}
```

Si ahora regresamos a la vista de build vamos a observar que ya solo nos falta 1 paso y es vincular nuestro frontend que acabamos desarrollar (si, también se llama frontend) con nuestro backend que vamos a desarrollar en Java. 

  ![alexa](https://raw.githubusercontent.com/windoctor7/windoctor7.github.io/master/static/img/checklist-endpoint.png)


# El backend

No explicaré paso a paso como desarrollar el backend en Java, en lugar de eso descarga el código fuente clonando el repositorio y abre el proyecto con tu IDE favorito. El proyecto ya contiene todo lo necesario para subirlo a Amazon Lambda, solo necesitas crear un JAR, dado que el proyecto está configurado con Gradle, solo necesitas correr la tarea **shadowJar** para generar el artefacto, el proyecto ya está configurado para que te aparezca esta tarea la cual genera un JAR con dependencias.

  ![alexa](https://raw.githubusercontent.com/windoctor7/windoctor7.github.io/master/static/img/shadow-jar.png)

Dado que el JAR generado pesa más de 10 MB, será necesario alojar el JAR en Amazon S3 debido a que Amazon Lambda no permite componentes mayores a 10MB.

Así pues, ingresa a la [Consola de Administración de AWS](http://console.aws.amazon.com/) y buscar el servicio **S3** asi como se muestra en la imagen

  ![alexa](https://raw.githubusercontent.com/windoctor7/windoctor7.github.io/master/static/img/s3-search.png)


Ingresa a la opción y luego localiza el botón de "Crear bucket" y establece un nombre el cual debe ser único, por ejemplo yo lo nombre como "calculadora-ask", el resto de valores dejalos tal cual están, incluyendo el de región EE.UU. Tendrás el bucket creado, da click sobre su nombre para entrar y cargar el JAR.

  ![alexa](https://raw.githubusercontent.com/windoctor7/windoctor7.github.io/master/static/img/bucket-s3.png)

Para cargar el jar solo da clik al botón cargar, busca el JAR y a todo da siguiente hasta que el archivo esté cargado. Al finalizar verás al archivo cargado, ahora da click sobre su nombre para que puedas obtener su url, copiala porque la vas a necesitar en el siguiente paso

  ![alexa](https://raw.githubusercontent.com/windoctor7/windoctor7.github.io/master/static/img/url-s3.png)

<hr />

Ahora debes crear una nueva función Lambda, dirigete nuevamente a la [consola](http://console.aws.amazon.com/) y busca ahora el servicio de Amazon Lambda, luego da click en **Create Function** y completa los siguientes pasos

  ![alexa](https://raw.githubusercontent.com/windoctor7/windoctor7.github.io/master/static/img/calculator-function-create.png)

Estamos a solo unos pasos de completar nuestra primer skill para Alexa. Amazon Lambda es una plataforma que puede ejecutar un código determinado cuando ocurre un evento, por lo tanto para nuestra función recien creada debemos vincularla al JAR creado y agregar un **desencadenador**, es decir, el evento que desencadenará que el código en el JAR se ejecute. Para ello, sigue los siguientes pasos que ves en la imagen:

  ![alexa](https://raw.githubusercontent.com/windoctor7/windoctor7.github.io/master/static/img/configuracion-funcion.png)

**Paso 1. Da click en "Añadir un desencadenador"**: Busca **Alexa Skills Kit**, vas a necesitar el ID de la skill alexa que creamos al inicio, para ello abre la consola de alexa en otra ventana, localiza el nombre de tu skill y en **View Skill ID** obtendrás el ID.

  ![alexa](https://raw.githubusercontent.com/windoctor7/windoctor7.github.io/master/static/img/skill-id.png)

Ahora pega el ID de la skill 

  ![alexa](https://raw.githubusercontent.com/windoctor7/windoctor7.github.io/master/static/img/desencadenador-lambda.png)
**Paso 2. Sube el código de la función:** Vuelve a entrar a la configuración de la función lambda que estamos creando, ya verás un desencadenador que es la skill alexa. Ahora en la sección de **Código de la función** llena los valores como se muestran en la imagen, en el campo controlador escribe ``windoctor7.github.io.alexa.skills.calculadora.MainStreamHandler`` y pega la URL que obtuviste al subir el JAR en Amazon S3 (te dije que la copiaras ;) )

  ![alexa](https://raw.githubusercontent.com/windoctor7/windoctor7.github.io/master/static/img/codigo-lambda.png)

Finalmente da click al botón Guardar.

En la esquina superior derecha, encontrarás un código ARN, la leyenda dice algo así:

    ARN - arn:aws:lambda:XXXXXXXXXXXXXXXXXXXXX

Copia este código ya que ahora deberemos conectar nuestra skill alexa (frontend) con nuestro backend para que sume los números.

Regresa a la consola de alexa, recuerdas que nos faltaba un punto final, el ENDPOINT, da click en la opción

  ![alexa](https://raw.githubusercontent.com/windoctor7/windoctor7.github.io/master/static/img/checklist-endpoint.png)

Solo debes elegir la opción AWS Lambda ARN y en el campo **Default Region** pegar el código ARN que te comenté arriba.

  ![alexa](https://raw.githubusercontent.com/windoctor7/windoctor7.github.io/master/static/img/arn-alexa.png)


# A probar la Skill!!!

Listo!!! Finalmente llega el momento de probar nuestra skill así que para ello vamos a probarla primero desde la consola y si todo funciona bien la probaremos desde nuestro dispositivo físico.

Dirígete a la opción Test, elige el idioma Español y escribe **"abre dime la suma"** (este es nuestro Invocation Name recuerdas?)

  ![alexa](https://raw.githubusercontent.com/windoctor7/windoctor7.github.io/master/static/img/test1-skill.png)

  Si todo sale bien, Alexa deberá darte el mensaje de bienvenida, luego escribe "suma ocho mas doce" y pulsa la tecla ENTER, verás que Alexa te dice el resultado:

![alexa](https://raw.githubusercontent.com/windoctor7/windoctor7.github.io/master/static/img/test2-skill.png)

Para probar nuestra nueva Skill en Alexa, lo único que debes hacer es abrir la app de Alexa en tu celular con la misma cuenta con la que creaste la skill. Dirigete a **Skills y juegos** luego selecciona **Mis Skills** y en la categoría Desarrollador encontrarás tu apreciada y flamante Skill. Ingresa a la Skill y solamente activala... listo! Has la prueba y continúa jugando a agregar nueva funcionalidad.

![alexa](https://raw.githubusercontent.com/windoctor7/windoctor7.github.io/master/static/img/app-alexa.png)

Aqui pueden ver el video demostrativo en el dispositivo físico.
<iframe width="560" height="315" src="https://www.youtube.com/embed/CflDEOMWtrw" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
