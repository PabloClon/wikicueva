---
title: Variable Aleatoria Discreta
---

# Variable Aleatoria Discreta

Estudiamos un experimento aleatorio, y para cada resultado me quedo con una característica que sea un número entero.

De los resultados siempre debo conocer:

```r
val  <- c(-1,   0,   2)  # Posibles valores
prob <- c(2/6, 3/6, 1/6) # Probabilidades de cada elemento

plot(val, prob, type="l")
```

![Variable Aleatoria Discreta: lineal][discreta-1]

Una vez que los tengamos, podemos calcular ciertas propiedades:

```r
media          <- sum(val * prob)
varianza       <- sum(((val - media) ** 2) * prob)
desv_tipica    <- sqrt(varianza)
prob_acumulada <- cumsum(prob)

# Representamos con gráfica escalonada
plot(val, prob_acumulada, type="s")
```

![Variable Aleatoria Discreta: escalonada][discreta-2]

# Variables Notables

Vamos a ver cinco variables notables. En la primera de ellas, todos los sucesos tienen la misma probabilidad. En las otras cuatro hay un suceso que nos interesa y conocemos su probabilidad:

* Me interesa solamente si se da o no
* ¿Cuántos intentos tienen que darse para que ocurra ese suceso?
* Si lo hago varias veces, ¿cuántas veces ocurre ese suceso?
* No conozco la probabilidad y me interesa cuántas veces ocurre ese suceso

### Trabajando en RStudio

Podemos declarar las variables notables con el siguiente formato:

```
Ctipo(val, n, p)
```

Siendo ```C``` una de las siguientes constantes:

* ```d```: Calcular probabilidades: \\( P(x = y) \\)
* ```p```: Calcular probabilidades: \\( P(x \leq y) \\)
* ```q```: Calcular percentiles
* ```r```: Funcionar como un *random*

Y los parámetros:

* ```val```: valores que proporcionamos
* ```n```: número de sucesos
* ```p```: la probabilidad dada

Por ejemplo:

```r
prob <- dbinom(val, n, p)
```

## Uniforme

Número de valores finito y todos con la misma probabilidad.

#### Ejemplo

> Tiro un dado y me interesa el número que sale.

```r
n <- 6

val  <- 1:n
prob <- rep(1/n, n)

m   <- sum(val * prob)       # Media aritmética
var <- sum((val-m)^2 * prob) # Varianza
sd  <- sqrt(var)             # Desviación típica

plot(val, prob)               # El dibujo es plano
```

![Variables Notables: Uniforme][uniforme-1]

## Bernouilli

Hay un suceso que me interesa y conozco su probabilidad \\( p \\). Se trata de una variable booleana:

```r
# P es la probabilidad conocida

# 0: no se cumple
# 1: se cumple

val  <- c(0, 1)
prob <- c(1 - P, P)

m   <- P
var <- P * (1 - P)
```

#### Ejemplo

> La probabilidad de que funcione un USB es del 80%

Podemos definir la x como:

* Funciona: 1
* No funciona: 0

\\( Valores = \{0, 1\} \\)  
\\( Probabilidades = \{0.2, 0.8\} \\)

Y podemos calcular:

\\( media = 0.8 \\)  
\\( varianza = 0.2 \cdot 0.8 = 0.16 \\)  
\\( desviación\ típica = \sqrt{0.16} = 0.4 \\)

## Variable binomial

Si el mismo experimento lo hacemos \\( n \\) veces, tenemos que \\( x \\) es el número de veces que ocurre el suceso que me interesa.

Escrito matemáticamente: \\( x = B(n, p) \\), es decir, \\( x \\) es una variable Binomial, con \\( n \\) sucesos y \\( p \\) la probabilidad dada.

El histograma esperado es una campana de Gauss.

```r
val  <- 0:n
prob <- dbinom(val, n, p)

m   <- n * p
var <- n * p * (1 - p)

FX  <- pbinom(val, n, p)
```

#### Ejemplo

> La probabilidad de que un USB falle es del 20%. Tengo 5000 USBs. Me interesa el número de los que fallan.

Pasos a seguir:

1. Analiza la variable
2. Calcula \\( m - sd \\) y \\( m + sd \\)
3. Calcula \\( P(x \geq m + sd) \\)
4. Calcula \\( P(m - sd \leq x \leq m + sd) \\)
5. Encuentra un intervalo donde creas que está \\( x \\)

```r
# 1.- Analizamos la variable
n <- 5000
p <- 0.2

val  <- 0:n
prob <- dbinom(val, n, p)

m  <- n * p
va <- n*p*(1-p)
sd <- sqrt(va)

## La dibujamos
plot(val, prob, type="l")
```

![Variables Notables: Binomial][binom-1]

```r
# 2.- Calculamos m-sd y m+sd
m - sd # 971.7157
m + sd # 1028.284

# 3.- Calculamos P(x >= m + sd) = P(x > 1028) = 1 - P(x <= 1028)
1 - pbinom(1028, n, p)

# 4.- Calculamos P(m - sd <= x <= m + sd) = P(971 < x <= 1028) = P(x <= 1028) - P(x <= 971)
pbinom(1028, n, p) - pbinom(971, n, p)
```

Para el último paso tenemos que encontrar dos números, \\( a \\) y \\( b \\), que dejan entre ellos el porcentaje de aciertos con seguridad (si no nos indican ningún valor, utilizaremos el 95%).

Haremos uso de percentiles: \\( a \\) es un percentil del 2.5% y \\( b \\) es el percentil del 75.5%

```r
# 5 .- Ajustamos mejor el intervalo
a <- qbinom(0.25/100, n, p)

b <- qbinom(97.25/100, n, p)
```

A la vista de los resultados, vemos claramente que los USBs rotos se encuentran en el rango \\( [921, 1055] \\).

> Nota: si la cantidad se sale de este rango, muy posiblemente nos estén engañando, e incluso se podría calcular la probabilidad de que esto esté sucediendo.

## Poisson

**No conocemos la probabilidad** de que se dé un suceso, pero nos interesa la cantidad de veces que ocurre.

Deben darse las siguientes condiciones:

* Conocemos, en media, cuántas veces ocurre ese suceso en un intervalo de tiempo (representado con \\( \lambda \\))
* La media es escalable (doble de intervalos = doble de casos)

\\( Valores = [0, +\infty) \\)

Escrito matemáticamente: \\( x = P(\lambda) \\), es decir, \\( x \\) es una variable de Poisson, siendo \\( \lambda \\) la media dada.

El histograma esperado es una campana de Gauss.

```r
# lambda es el valor de la media

val  <- 0:Inf
prob <- dpois(val, lambda)

media    <- lambda
varianza <- lambda
```

#### Ejemplo

> En un sistema de atención al público, llegan, en media, 30 USBs rotos al mes. Nos interesa la cantidad de USBs rotos el próximo mes, con una seguridad del 90%.

Pasos a seguir:

1. Analiza la variable
2. Calcula \\( m - sd \\) y \\( m + sd \\)
3. Calcula \\( P(m - sd \leq x \leq m + sd) \\)
4. Encuentra un intervalo donde creas que está \\( x \\)

Como es lógico, no podemos trabajar con un intervalo de cero a infinito, por lo cual estableceremos el intervalo de estudio:

Sabemos que \\( m = 80 \\), \\( var = 80 \\) y por tanto, \\( sd = \sqrt{80} \approx 9 \\). Trabajaremos con el intervalo \\( [0, m + 2\cdot sd] \\), es decir, \\( [0, 98] \\)

```r
l = 80

val  <- 0:98
prob <- dpois(val, l)

plot(val, prob, type="l")
```

![Variables Notables: Poisson][poisson-1]

Al realizar el dibujo, podemos ver claramente que hemos cortado muy pronto, por lo que aumentamos el intervalo hasta \\( [0, m + 3 \cdot sd] \\):

```r
l = 80

val  <- 0:107
prob <- dpois(val, l)

plot(val, prob, type="l")
```

![Variables Notables: Poisson][poisson-2]

Ahora podemos continuar con el ejercicio:

```r
# 3.- Calcular P(71 <= x <= 89) = P(x <= 89) - P(x <= 70)
ppois(89, l) - ppois(70, l)

# 4 .- Ajustamos mejor el intervalo
a <- qpois(0.25 / 100, l)
b <- qpois(97.25 / 100, l)
```

#### Nota

Binomial y Poisson sirven para contar, por tanto el dibujo es el mismo.

> Si conozco la probabilidad del suceso, se trata de binomial, en caso contrario, de Poisson.

## Geométrica

Repetimos un experimento hasta que ocurra un suceso que queremos estudiar, y nos interesa la cantidad de fallos hasta dicho suceso. Si la probabilidad del suceso no varía con el tiempo, se trata de una variable geométrica.

\\( Valores = [0, +\infty) \\)

Escrito matemáticamente: \\( x = G(p) \\), es decir, \\( x \\) es una variable Geométrica, y \\( p \\) es la probabilidad del suceso.

\\( media = \frac{1\ -\ p}{p} \\)  
\\( varianza = \frac{1\ -\ p}{p^2} \\)

El histograma esperado es una curva decreciente con asíntota en el eje X.

**Observación**: *la geométrica no tiene memoria*. Como \\( p \\) no depende del tiempo, la probabilidad de un suceso después de muchos fallos, es la misma que al comenzar.

```r
val  <- 0:Inf
prob <- dgeom(val, p)

media    <- (1 - p) / p
varianza <- (1 - p) / p**2
```

[discreta-1]: /uploads/informatica/2/est/variable-aleatoria-discreta-1.png
[discreta-2]: /uploads/informatica/2/est/variable-aleatoria-discreta-2.png
[uniforme-1]: /uploads/informatica/2/est/variable-notable-uniforme-1.png
[binom-1]: /uploads/informatica/2/est/variable-notable-binom-1.png
[poisson-1]: /uploads/informatica/2/est/variable-notable-poisson-1.png
[poisson-2]: /uploads/informatica/2/est/variable-notable-poisson-2.png