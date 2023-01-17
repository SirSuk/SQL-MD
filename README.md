SQL deadpool
Estos apuntes son un resumen del video de 3 horas de Mosh Hamedani.
https://www.youtube.com/watch?v=7S_tz1z_5bA

Son ejemplos de una base de datos real, en inglés, pondré pequeñas tablas para que entendamos el contexto e intentaré explicar lo mejor posible, es mi primera experiencia con algo asi que espero que os sirva!

Vamos a usar una base de datos:

USE sql_store
La misma tendrá dentro varias tablas, como customers, por ejemplo.

TABLA CUSTOMERS
customer_id	first_name	last_name	birth_date	points	country	state
2	Jose	Lopez	1986-03-28	2500	'US'	'VA'
3	Graham	Gomez	1990-01-01	1500	'US'	'FL'
El siguiente comando nos la tabla completa:

SELECT * FROM customers
Ahora solo quiero el que tenga la id 26

SELECT * FROM customers WHERE customer_id = 26
Ahora quiero que me de la tabla ordenada por apellidos, facil:

SELECT * FROM customers ORDER BY first_name
Puedes comentar una linea poniendo delante --, eso si, solo funciona si estas trabajando con varias:

SELECT * FROM customers 
  --WHERE customer_id = 26 
    ORDER BY name
Ten cuidado hermano deadpool, el orden importa, por tanto esto NO sería valido:

SELECT * FROM customers 
ORDER BY customer_id 
WHERE country="US" 
SELECT
 Vamos a jugar con el select, no siempre será un asterisco, no?

Pidamos solo nombres y apellidos:

SELECT first_name, last_name 
FROM customers 
Ahora tambien quiero los puntos de estos clientes:

SELECT first_name, last_name, points 
FROM customers 
Una vuelta de tuerca mas, quiero añadirles 10 puntos a todos

SELECT first_name, last_name, points + 10 
FROM customers 
Vamos con un ejemplo del uso de los numeros, en este caso multiplicamos los puntos por 10 primero y luego les sumamos el 100, ya que la multiplicacion tiene preferencia:

SELECT first_name, 
       last_name, 
       points, 
       points * 10 + 100 
FROM customers 
En este otro caso, primero se multiplican los numeros 10 y 100 y luego se suma el valor de points:

SELECT first_name, 
       last_name, 
       points, 
       points + 10 * 100 
FROM customers 
Cambiaremos preferencias con paréntesis:

SELECT first_name, 
       last_name, 
       points, 
       (points * 10) + 100 
FROM customers 
El ejemplo anterior tiene un problemilla, el nombre de nuestro campo será (points * 10) + 100, y, obviamente no queremos un nombre asi no?? Vamos con la solucion a esto:

SELECT first_name, 
       last_name, 
       points, 
       (points * 10) + 100 AS 'discount factor'
FROM customers 
Duplicados
Imaginad que buscamos los estados de los que son originarios nuestros clientes:

SELECT state
FROM customers 
Esto me esta devolviendo estados repetidos, obviamente tengo varios clientes de Florida y otros tantos de Colorado. Vamos a solucionarlo easy:

SELECT DISTINCT state
FROM customers 
La anterior sentencia me devuelve solo una vez el estado, asi tendremos estados únicos, para que mas..

Imaginaos que os pido que me deis name, unit_price, y un nuevo unit_price multiplicado por 2 de la tabla productos:

SELECT * name, unit_price, unit_price*2 AS newprice  FROM customers 
WHERE
 Empezamos a establecer condiciones con nuestro querido WHERE

Quiero los clientes con mas de 3000 puntos y que sean de Virginia.

SELECT * FROM customers 
WHERE points > 3000 
AND state ='VA'
Recodad que sql es CASE INSENSITIVE, esto funciona también:

SELECT * FROM customers 
WHERE points > 3000 
AND state ='va'
Ahora quiero los que no son de Virginia:

SELECT * FROM customers 
WHERE points > 3000 
AND state !='va'
Y esto es otra forma de hacer lo mismo:

SELECT * FROM customers 
WHERE points > 3000 
AND state <> 'va'
Vamos a trabajar un poco con fechas, recuerda que el formato establecido es:

YYYY-MM-DD
Año 4 cifras, mes y dia 2 cifras.
Por ejemplo: 1990-01-03
Dicho esto, quiero solo los que hayan nacido despues del año 2000:

SELECT * FROM customers 
WHERE birth_date > '2000-01-01' 
IMPORTANTE: Esto devolverá los customers nacidos en cualquier fecha posterior a esta.

AND OR Y NOT
 Creo que todos conocemos lo básico de estos tres, asique vamos a mezclarlos

SELECT * FROM customers 
WHERE birth_date > '2000-01-01' OR points > 1000 AND state = 'VA'
Recibimos customers nacidos despues del 1 de Enero del 2000, OR customers de Virginia con mas de 1000 puntos

El uso del paréntesis puede aclararte mejor las cosas:

SELECT * FROM customers 
WHERE birth_date > '2000-01-01' 
OR (points > 1000 AND state = 'VA')
Vamos a pedir los nacidos antes del 90 o que tengan menos de 1000 puntos, pero negando:

SELECT * FROM customers 
WHERE NOT (birth_date > '2000-01-01' OR points > 1000) 
IN
Imaginemos que quiero customers de varios estados:

SELECT * FROM customers 
WHERE state = 'VA' OR state = 'GA' OR state = 'FL'
Esto en SQL se soluciona con el IN, obteniendo el mismo resultado:

SELECT * FROM customers 
WHERE state IN('VA','GA','FL')
Si lo negamos, tenemos customers de los otros estados:

SELECT * FROM customers 
WHERE state NOT IN('VA','GA','FL')
BETWEEN
Dame los clientes que tienen entre 1000 y 3000 puntos:

SELECT * FROM customers 
WHERE points >= 1000 AND points <=3000
Ahora con between, mucho mas limpio:

SELECT * FROM customers 
WHERE points BETWEEN 1000 AND 3000
Si queremos los nacidos entre dos fechas por ejemplo, usamos comillas:

SELECT * FROM customers 
WHERE birth_date BETWEEN '1990-01-01' AND '2000-01-01'
LIKE
Imaginate que me piden los customers cuyo apellido empieza con B

SELECT * FROM customers 
WHERE last_name LIKE 'b%'
Podemos colocar este signo % de la manera que mejor se adapte a lo que necesitamos.

Customers con una letra S en su apellido, me da igual donde:

SELECT * FROM customers 
WHERE last_name LIKE '%s%'
O customers cuyo apellido no tenga una S:

SELECT * FROM customers 
WHERE last_name NOT LIKE '%s%'
Quiero clientes con apellidos de 5 letras y que terminen en Y:

SELECT * FROM customers 
WHERE last_name LIKE '____y'
Ahora clientes con apellidos de 8 letras, que comienzan en B y terminan en Y.

SELECT * FROM customers 
WHERE last_name LIKE 'b______y'
Ahora clientes con apellidos de 8 letras, que comienzan en B y terminan en Y.

SELECT * FROM customers 
WHERE adress LIKE 'b______y'
Queremos apellidos que contengan el string 'field':

SELECT * FROM customers 
WHERE last name LIKE '%field%'
REGEXP es mucho mas potente,y nos permite evitar el uso de esta nomenclatura, lo que va dentro tiene sus propias normas, explicaremos algunas de las mas basicas:

SELECT * FROM customers 
WHERE last name REGEXP 'field'
Nos piden apellidos que empiezen con G... En regex tenemos caracteres especiales para jugar, por ejemplo la 'cedilla' (^) , nos dice que la palabra tiene que empezar con 'G':

SELECT * FROM customers 
WHERE last name REGEXP '^g' --Gomez,Gutierrez
o el dolar al final para apellidos que terminen en o

SELECT * FROM customers 
WHERE last name REGEXP 'o$' --
Podemos usar PIPES para elegir unos u otros, como en js.

Queremos nombres que tengan algo de esto, da igual donde:

SELECT * FROM customers 
WHERE last name REGEXP 'MA|ME|HU' 
O tal vez necesitemos que empiezen con una de estas:

SELECT * FROM customers 
WHERE last name REGEXP '^MA|ME|HU' 
Vamos a explicar el uso de brackets []:

SELECT * FROM customers 
WHERE last name REGEXP '[gim]e'
En el caso de arriba me devuelve todos los apellidos que tengan GE, IE o ME, es decir, las letras E se puede combinar con cualquiera de las que estan en el paréntesis.

Imagina que quiero apellidos que tengan la letra e seguida de f,r,m , pues lo hacemos al revés:

SELECT * FROM customers 
WHERE last name REGEXP 'e[frm]' -- ge / ie / me
Ahora dame apellidos que tengan una F y delante de la misma cualquier letra entre la A y la H:

SELECT * FROM customers 
WHERE last name REGEXP '[a-h]F' -- ge / ie / me
NOTA:esto se puede complicar hasta el infinito, hay mil y una combinaciones posibles para cualquier cosa, asique el que quiera.... continuamos con SQL

IS NULL
 Muy utilizado, para buscar propiedades vacias

Dame clientes que no tengan teléfono:

SELECT * FROM customers 
WHERE phone IS NULL
Negandolo me da todos menos los vacios:

SELECT * FROM customers 
WHERE phone IS NOT NULL
Tengo una tienda online y necesito los pedidos que no han sido enviados aun. Trabajaré en mi tabla ORDERS, en la que tengo datos como shipped_date, shipper_id

SELECT * FROM orders
WHERE shipped_date IS NULL
ORDER BY
 Ordenar por, de forma descendente, ascendente etc

NOTA: Por defecto los datos vienen ordenados por ID, porque es la PRIMARY KEY

vamos a ordenar los customers por estado,y tambien por apellido.

SELECT * FROM customers 
ORDER BY state, first_name
El funcionamiento es sencillo, primero nos ordena por estados, pero si en este estado hay mas de un customer, ordenará estos por sus apellidos...

Vamos a complicarlo un poco, ahora queremos de la tabla solo nombre y apellido, y queremos ordenar por apellido, hay una forma corta de hacerlo:

SELECT first_name, last_name 
FROM customers 
ORDER BY 2,1
Curioso, no ? Me recuerda al método sort() de los arrays javascript, damos la vuelta al 1 y al 2 para elegir según los parametros de arriba..

Aun asi, no es recomendable porque puede producir resultados inesperados , solución:

SELECT first_name, last_name 
FROM customers 
ORDER BY last_name
Tenemos una tabla order_items:

order_id	product_id	quantity	unit_price
2	1	2	9.10
2	4	4	1.66
2	6	2	2.94
Basandonos en estos datos, imaginad que nos piden que devolvamos los registros con order_id 2 , y que mostremos su precio total, ordenados de forma descendente:

SELECT *, quantity * unit_price AS total_price
FROM order_items
WHERE order_id = 2 
ORDER BY quantity * unit_price DESC
Si os fijais, estamos creando el campo total_price, al cual le asignamos la multiplicacion de cantidad x precio unitario.

Esto lo podemos refactorizar, en la ultima linea usamos el total_price que hemos creado:

SELECT *, quantity * unit_price AS total_price
FROM order_items
WHERE order_id = 2 
ORDER BY total_price DESC
LIMIT
 Queremos limitar los resultado obtenidos

Quiero que me devuelvas solo 3 clientes

SELECT * 
FROM customers 
LIMIT 3
Quiero paginar mi web, y obtener por ejemplo 3 registros por cada una de las paginas( pagina 1 : del 3 pagina 2 : del 4 a la 6 pagina 3 : del 7 a la 9 )

SELECT * 
FROM customers 
LIMIT 6,3
En el caso de arriba al 6 se le llama "offset", le estamos diciendo que lo omita y que me de los 3 valores siguientes (7,8,9)

Nos preguntan por los 3 customers mas leales (o con mas puntos )

SELECT * 
FROM customers 
ORDER BY points DESC
LIMIT 3
ATENCION: Limit siempre va al final, si no respetamos el orden las querys no funcionarán correctamente

INNER JOIN
 En la vida real no solo seleccionas datos de una tabla, sino que puedes unir varias 

Al principio hemos trabajado con nuestra tabla customers, que tenia datos como el id, nombre,cumpleaños, los puntos, etc :

TABLA CUSTOMERS
customer_id	first_name	last_name	birth_date	points	country	state
2	Jose	Lopez	1986-03-28	2500	'US'	'VA'
3	Graham	Gomez	1990-01-01	1500	'US'	'FL'
Ahora vamos a trabajar con nuestra tabla orders:

TABLA ORDERS
order_id	customer_id	order_date
2	1	2
2	4	4
2	6	2
INFO: Si un cliente varia sus puntos, por ejemplo, los varia en la tabla customers, accedemos a ella desde ORDERS para lo que necesitemos, si estuvieran los datos en ambas nos volveriamos locos. Por eso tenemos tablas separadas

En el INNER JOIN ( el INNER es opcional, podemos omitirlo)

EXPLICACION: Usamos el customer_id de esta tabla(ORDERS) para acceder a datos como birthdate,last_name etc de la tabla customers

Vamos a empezar a combinar estos datos de customers y orders:

SELECT * 
FROM orders 
JOIN customers 
ON orders.customer_id = customers.customer_id
Aqui arriba le estamos diciendo: !Oye, uneme las tablas y asegurate de que los customer_id sean iguales en ambas!

SELECT order_id, customer_id, first
