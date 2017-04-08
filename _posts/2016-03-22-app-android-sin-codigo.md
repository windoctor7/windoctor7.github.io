---
layout: post
title: App Android para publicar tweets sin una línea de código con MIT App Inventor
tags:
- Android
- App Inventor
---

![MIT APP Inventor](https://raw.githubusercontent.com/windoctor/windoctor.github.io/master/static/img/mitappinventor.png)

[MIT App Inventor](http://appinventor.mit.edu/explore/) es un proyecto experimental desarrollado por profesores del Tecnológico de Massachusetts que permite desarrollar diversos tipos de apps android sin escribir una sóla línea de código! Así es! Todo mediante pequeños bloques que debemos conectar entre sí para crear flujos y acciones! Cosa de niños!

### Dando de alta nuestra aplicación
Primeramente debemos dar de alta nuestra aplicación en el [Twitter Application Management](https://apps.twitter.com/)
La creación me parece sencilla, hay un botón Create New App que nos pedirá el Nombre, la descripción y website de nuestra aplicación, podemos poner cualquiera, procurando que el nombre sea algo personalizado o de lo contrario nos dirá que ya existe una app con dicho nombre.

Los datos se llenan más o menos de la siguiente forma ![Twitter Application Management](https://raw.githubusercontent.com/windoctor/windoctor.github.io/master/static/img/app_management_twitter.png)

Cabe mencionar que en el apartado de <strong>Callback URL</strong> debemos ingresar un sitio web, que puede ser el mismo del website o de lo contrario obtendrémos un error al querer realizar la autenticación desde nuestra app.

Hecho lo anterior, vamos a tener 2 datos importantísimos que ocuparemos más adelante.

* <strong>Consumer Key (API Key)</strong>
* <strong>Consumer Secret (API Secret)</strong>

### Preparando App Inventor
Teniendo una cuenta de Google, debemos ingresar al [dashboard de app inventor](http://ai2.appinventor.mit.edu/)
App Inventor hace una pequeña mágia através que nuestra computadora y smartphone estén conectados a la misma red WIFI, para ello descarga la aplicación [MIT AI2 Companion](https://play.google.com/store/apps/details?id=edu.mit.appinventor.aicompanion3) desde el PlayStore.

Una vez descargada dicha app en nuestro móvil la abrimos y desde nuestra computadora en nuestro panel de diseño seleccionamos conectarnos mediante AI Companion

![Connect](https://raw.githubusercontent.com/windoctor/windoctor.github.io/master/static/img/Connect_AI_Companion.png)

Con ello nos aparecerá un código QR que podemos leer desde la app MIT AI2 Companion que acabamos de descargar.

Finalmente tenemos sincronizado el panel de diseño con nuestro móvil y conforme vayamos agregando componentes a nuestro panel, éstos se verán reflejados automáticamente en nuestro móvil.


### Comenzando con el ejemplo
Como pueden observar, tenemos nuestra paleta de componentes del lado izquierdo. Vamos a arrastrar en el siguiente órden estos componentes:

* Button
* TextBox
* Button
* Twitter
* Notifier

La caja de texto y el último botón deben modificar su propiedad "Visible" en el panel "Properties" que se encuentra del lado derecho.

Nos debe quedar algo como esto:

![Design](https://raw.githubusercontent.com/windoctor/windoctor.github.io/master/static/img/design_twitter_appinventor.png)

Finalmente, en la esquina superior derecha tenemos la opción de cambiar nuestra vista "Designer" a "Blocks"

Podemos ver que tenemos 4 segmentos de bloques, en el primero definimos unas variables y sus valores. Para ello utilizamos el apartado "Variables" y "Text" para construir los bloques del primer segmento que vemos en naranja y guinda. En el "Consumer Key" y "Consumer Secret" van los valores que obtuvimos al dar de alta la app en el Twitter Application Management.

![Block](https://raw.githubusercontent.com/windoctor/windoctor.github.io/master/static/img/block_diagrams_twitter.png)


Me parece que queda del lado de ustedes el experimentar un poco y armar todo el bloque como se observa en la imagen.

Pueden ver [aquí el video](https://www.youtube.com/watch?v=v3ycJsrSJf4) que subí de la mini app funcionando en mi móvil.

La intención es meramente didáctica con el fin de despertar el interés de alguno de ustedes.

Saludos!!




