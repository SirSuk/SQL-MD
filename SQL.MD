<!-- Esto es un comentario -->

<!-- Cabeceras -->
# <code>SQL deadpool alternative auto-teach</code>
#### Estos apuntes son un resumen del video de 3 horas de Mosh Hamedani. 
https://www.youtube.com/watch?v=7S_tz1z_5bA
***

<!-- Texto normal -->
Son ejemplos de una base de datos real, en inglés, y no vais a ver la tabla, asique intentaré explicar lo mejor posible, es mi primera experiencia con algo asi que espero que os sirva!

---
Vamos a usar una base de datos:
```sql
USE sql_store
```
La misma tendrá dentro varias tablas, como customers, por ejemplo.
El siguiente comando nos la devuelve:

```sql
SELECT * FROM customers
```
Ahora solo quiero el que tenga la id 26
```sql
SELECT * FROM customers WHERE customer_id = 26
```
Ahora quiero que me de la tabla ordenada por apellidos, facil:
```sql
SELECT * FROM customers ORDER BY first_name
```
Puedes comentar una linea poniendo delante --, eso si, solo funciona si estas trabajando con varias:

```sql
SELECT * FROM customers 
  --WHERE customer_id = 26 
    ORDER BY name
```


Ten cuidado hermano deadpool, el orden importa, por tanto esto NO sería valido:

```sql
SELECT * FROM customers 
ORDER BY customer_id 
WHERE country="US" 
```

---

## SELECT

<code> Vamos a jugar con el select, no siempre será un asterisco, no?</code>

Pidamos solo nombres y apellidos:

```sql
SELECT first_name, last_name 
FROM customers 
```

Ahora tambien quiero los puntos de estos clientes:

```sql
SELECT first_name, last_name, points 
FROM customers 
```
Una vuelta de tuerca mas, quiero añadirles 10 puntos a todos


```sql
SELECT first_name, last_name, points + 10 
FROM customers 
```

Vamos con un ejemplo del uso de los numeros, en este caso multiplicamos los puntos por 10 primero y luego les sumamos el 100, ya que la multiplicacion tiene preferencia:

```sql
SELECT first_name, 
       last_name, 
       points, 
       points * 10 + 100 
FROM customers 
```
En este otro caso, primero se multiplican los numeros 10 y 100 y luego se suma el valor de points:

```sql
SELECT first_name, 
       last_name, 
       points, 
       points + 10 * 100 
FROM customers 
```

Cambiaremos preferencias con paréntesis:
```sql
SELECT first_name, 
       last_name, 
       points, 
       (points * 10) + 100 
FROM customers 
```

El ejemplo anterior tiene un problemilla, el nombre de nuestro campo será (points * 10) + 100, y, obviamente no queremos un nombre asi no?? 
Vamos con la solucion a esto:
```sql
SELECT first_name, 
       last_name, 
       points, 
       (points * 10) + 100 AS 'discount factor'
FROM customers 
```

<code><b>Duplicados</b></code>
---

Imaginad que buscamos los estados de los que son originarios nuestros clientes:
```sql
SELECT state
FROM customers 
```

Esto me esta devolviendo estados repetidos, obviamente tengo varios clientes de Florida y otros tantos de Colorado.
Vamos a solucionarlo easy:

```sql
SELECT DISTINCT state
FROM customers 
```
La anterior sentencia me devuelve solo una vez el estado, asi tendremos estados únicos, para que mas..

---

Imaginaos que os pido que me deis name, unit_price, y un nuevo unit_price multiplicado por 2 de la tabla productos:

```sql
SELECT * name, unit_price, unit_price*2 AS newprice  FROM customers 
```

---
## WHERE

<code> Empezamos a establecer condiciones con nuestro querido WHERE</code>

Quiero los clientes con mas de 3000 puntos y que sean de Virginia.
```sql
SELECT * FROM customers 
WHERE points > 3000 
AND state ='VA'
```
Recodad que sql es CASE INSENSITIVE, esto funciona también:
```sql
SELECT * FROM customers 
WHERE points > 3000 
AND state ='va'
```
Ahora quiero los que no son de Virginia:
```sql
SELECT * FROM customers 
WHERE points > 3000 
AND state !='va'
```
Y esto es otra forma de hacer lo mismo:
```sql
SELECT * FROM customers 
WHERE points > 3000 
AND state <> 'va'
```
Vamos a trabajar un poco con fechas, recuerda que el formato establecido es:
- YYYY-MM-DD 
- Año 4 cifras, mes y dia 2 cifras.
- Por ejemplo:  1990-01-03

Dicho esto, quiero solo los que hayan nacido despues del año 2000:

```sql
SELECT * FROM customers 
WHERE birth_date > '2000-01-01' 

```
> <b>IMPORTANTE</b>: Esto devolverá los customers nacidos en cualquier fecha posterior a esta.
---




## AND OR Y NOT

<code> Creo que todos conocemos lo básico de estos tres, asique vamos a mezclarlos</code>


```sql
SELECT * FROM customers 
WHERE birth_date > '2000-01-01' OR points > 1000 AND state = 'VA'
```
Recibimos customers nacidos despues del 1 de Enero del 2000, <b>OR</b> customers de Virginia con mas de 1000 puntos

---
El uso del paréntesis puede aclararte mejor las cosas:

```sql
SELECT * FROM customers 
WHERE birth_date > '2000-01-01' 
OR (points > 1000 AND state = 'VA')
```

---
Vamos a pedir los nacidos antes del 90 o que tengan menos de 1000 puntos, pero negando:

```sql
SELECT * FROM customers 
WHERE NOT (birth_date > '2000-01-01' OR points > 1000) 
```

## IN


Imaginemos que quiero customers de varios estados:
```sql
SELECT * FROM customers 
WHERE state = 'VA' OR state = 'GA' OR state = 'FL'
```

Esto en SQL se soluciona con el IN, obteniendo el mismo resultado:
```sql
SELECT * FROM customers 
WHERE state IN('VA','GA','FL')
```

Si lo negamos, tenemos customers de los otros estados:
```sql
SELECT * FROM customers 
WHERE state NOT IN('VA','GA','FL')
```

## BETWEEN
Dame los clientes que tienen entre 1000 y 3000 puntos:
```sql
SELECT * FROM customers 
WHERE points >= 1000 AND points <=3000
```
Ahora con between, mucho mas limpio:
```sql
SELECT * FROM customers 
WHERE points BETWEEN 1000 AND 3000
```

Si queremos los nacidos entre dos fechas por ejemplo, usamos comillas:

```sql
SELECT * FROM customers 
WHERE birth_date BETWEEN '1990-01-01' AND '2000-01-01'
```


## LIKE
Imaginate que me piden los customers cuyo apellido empieza con B
```sql
SELECT * FROM customers 
WHERE last_name LIKE 'b%'
```
Podemos colocar este signo % de la manera que mejor se adapte a lo que necesitamos.

Customers con una letra S en su apellido, me da igual donde:
```sql
SELECT * FROM customers 
WHERE last_name LIKE '%s%'
```
O customers cuyo apellido no tenga una S:
```sql
SELECT * FROM customers 
WHERE last_name NOT LIKE '%s%'
```


Quiero clientes con apellidos de 5 letras y que terminen en Y:
```sql
SELECT * FROM customers 
WHERE last_name LIKE '____y'
```

Ahora clientes con apellidos  de 8 letras, que comienzan en B y terminan en Y.
```sql
SELECT * FROM customers 
WHERE last_name LIKE 'b______y'
```

Ahora clientes con apellidos  de 8 letras, que comienzan en B y terminan en Y.
```sql
SELECT * FROM customers 
WHERE adress LIKE 'b______y'
```

Queremos apellidos que contengan el string 'field':
```sql
SELECT * FROM customers 
WHERE last name LIKE '%field%'
```

REGEXP es mucho mas potente,y nos permite evitar el uso de esta nomenclatura, a mi personalmente me recuerdan a los 'bac tics', como los llamaba nuestro amigo Ivan :( .

```sql
SELECT * FROM customers 
WHERE last name REGEXP 'field'
```
En regex tenemos caracteres especiales para jugar, por ejemplo la <b>'cedilla' (^) </b>, nos dice que la palabra tiene que empezar con field:

```sql
SELECT * FROM customers 
WHERE last name REGEXP 'field'
```
