---
layout: post
section-type: post
title: Introducción a R
category: rstudio
overview: Un vistazo al lenguaje R donde aplicaremos funciones estadísticas 
tags: ['rstudio' ]
image: https://raw.githubusercontent.com/windoctor7/windoctor7.github.io/master/static/img/pwa.png
source: https://github.com/windoctor7/codigo-tutoriales-blog/tree/master/spring-pwa
---

![R](/static/img/R_Project.png)

# ¿Qué es R?
Según la [wikipedia](https://es.wikipedia.org/wiki/R_(lenguaje_de_programación)): **"R es un entorno y lenguaje de programación con un enfoque al análisis estadístico"**, además de ser muy popular en los campos de Machile Learning, Minería de Datos y en la investigación biomédica.

Para seguir este tutorial, es requisito tener instalado el [entorno de R](https://www.r-project.org) y [RStudio](https://rstudio.com/products/rstudio/). Si usas Windows me parece que la instalación debe ser muy simple. En el caso de MAC también puedes descargar los instaladores por separado o usar Homebrew.

# Los datos
Partimos de una data que puedes descargar [aquí](https://raw.githubusercontent.com/windoctor7/codigo-tutoriales-blog/master/scripts_r/data/data_pp.csv). Esta data contiene "convenios de pago" que cierto banco le hace a sus clientes morosos. Existen 3 tipos de "convenios":

1. **Regularización:** Son para clientes que no tienen una mora muy alta y les permite ponerse "al corriente" con su crédito, es decir, normalizar su crédito.
1. **Liquidación:** Son para clientes con moras muy altas ó con plazos ya vencidos. En su mayoría se condona un importante porcentaje de moratorios para que el cliente pague y liquide su crédito.
1. **Reestructura:** Una reestructura que hacen la mayoría de los bancos.

El significado de algunos campos son:

- SEM_ATRASO: Son las semanas que tiene de atraso el cliente
- SALDO: Es el saldo deudor que tenia el cliente al momento de generarle el convenio. Por saldo deudor entendemos el capital + intereses.
- P_SEMANAL: Es el monto que el cliente debe pagar cada semana para mantener "vigente" su convenio de pago.

# Cargando los datos
Desde RStudio cargamos los datos

```r
pp <- read.csv("/Users/ascari/Library/Mobile Documents/com~apple~CloudDocs/Maestria/R/data/data_pp.csv")
```