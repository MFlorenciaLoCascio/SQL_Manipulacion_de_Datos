# Algunos de los ejercicios del curso de Manipulación de Datos en SQL

Puedes acceder al curso  [aquí](https://app.datacamp.com/learn/courses/data-manipulation-in-sql), tiene una duración de 4 horas, 15 videos y 55 ejercicios. 👩‍💻

Certificado  [aquí](https://www.datacamp.com/completed/statement-of-accomplishment/course/b562e32da02cd9acdbe3bbfafc45819ffc25c1bd)

## Descripción del curso:

Aprenderás varias funciones clave necesarias para organizar, filtrar y categorizar información en una base de datos relacional. El uso robusto de «CASE statements», subconsultas y «window functions» (funciones de ventana), todo mientras descubres algunos datos interesantes sobre el fútbol utilizando la base de datos europea de fútbol.

## 1️⃣ Tomaremos el «CASE»

Aprenderás a utilizar la instrucción CASE WHEN para crear variables categóricas, agregar datos en una sola columna con múltiples condiciones de filtrado y calcular recuentos y porcentajes.

### Declaraciones CASE Bascicas

-- Selecciona el nombre largo del equipo y la ID de API de la tabla teams_germany. Filtra la consulta para FC Schalke 04 y FC Bayern Munich usando IN, dándole la team_api_IDs necesaria para el siguiente paso.

```
SELECT
-- Selecciona el "team long name" y "team API id"
team_long_name, team_api_id
	
FROM teams_germany
-- Incluye solo FC Schalke 04 y FC Bayern Munich
WHERE team_long_name IN ('FC Schalke 04', 'FC Bayern Munich');
```

### Declaraciones CASE que comparan valores de columna

-- Selecciona el date del partido y crea una condición CASE para identificar partidos como victorias locales, derrotas locales o empates.

```
SELECT date,
-- Selecciona la fecha del partido
-- WHERE season = '2011/2012'
-- Identifica victoria y derrotas locales, y empates 
	 CASE WHEN home_goal > away_goal THEN 'Home win!'
         WHEN home_goal < away_goal THEN 'Home loss :(' 
         ELSE 'Tie' END AS outcome
FROM matches_spain;
```

-- Completa la condición CASE para identificar los partidos del equipo visitante del Barcelona (id = 8634) como victorias, derrotas o empates. Haz un «left join» de la tabla teams_spain con la tabla matches_spain utilizando las columnas team_api_id y hometeam_id. Esto devuelve la identidad del oponente del equipo local. Filtra la consulta para incluir solo partidos en los que el Barcelona fue el equipo visitante.

```
-- Selecciona equipo donde el Barcelona era el equipo visitante
SELECT  
	m.date,
	t.team_long_name AS opponent,
	 CASE WHEN m.home_goal < m.away_goal   THEN 'Barcelona win!'
        WHEN m.home_goal > m.away_goal THEN 'Barcelona loss :(' 
        ELSE 'Tie' END AS outcome
FROM matches_spain AS m
-- Haz join teams_spain con matches_spain
LEFT JOIN teams_spain AS t 
ON m.hometeam_id = t.team_api_id
WHERE m.awayteam_id = 8634;
```

-- Completa la primera condición CASE, identificando al Barcelona o al Real Madrid como el equipo local utilizando la columna hometeam_id. Completa la segunda condición CASE de la misma manera, usando awayteam_id.

```
SELECT 
    date,
    -- Identifica el equipo local como Barcelona o Real Madrid
    CASE 
        WHEN hometeam_id = 8634 THEN 'FC Barcelona' 
        WHEN hometeam_id = 8633 THEN 'Real Madrid CF'
    END AS home,
    
    -- Identifica el equipo visitante como Barcelona o Real Madrid
    CASE 
        WHEN awayteam_id = 8634 THEN 'FC Barcelona'
        WHEN awayteam_id = 8633 THEN 'Real Madrid CF'
    END AS away
FROM 
    matches_spain
WHERE 
    (awayteam_id = 8634 OR hometeam_id = 8634)
    AND (awayteam_id = 8633 OR hometeam_id = 8633);
```

### Filtrar condicion CASE

-- Identifica el ID del equipo de Bologna que figura en la tabla teams_italy seleccionando team_long_name y team_api_id.

```
-- Selecciona team_long_name y team_api_id de team
SELECT
	team_long_name,
	team_api_id
FROM teams_italy
-- Filtra por team long name
WHERE team_long_name = 'Bologna';
```


### COUNT usando CASE WHEN

-- Crea una condición CASE que identifica la id de partidos disputados en la temporada 2012/2013. Especifica que quieres que los valores ELSE sean NULL. Envuelve la condición CASE en una función COUNT y agrupa la consulta con el alias country.

```
SELECT 
	c.name AS country,
-- Cuenta partidos de la temporada 2012/2013
	COUNT(CASE WHEN m.season = '2012/2013' THEN m.id ELSE NULL END) AS matches_2012_2013
FROM country AS c
LEFT JOIN match AS m
ON c.id = m.country_id
-- Group by country name alias
GROUP BY country;
```

### COUNT y CASE WHEN con múltiples condiciones

-- Crea 3 condiciones CASE para "contar" coincidencias en las temporadas '2012/2013', '2013/2014', y '2014/2015', respectivamente. Haz que la condición CASE devuelva un 1 para cada partido que quieras incluir, y un 0 para cada partido a excluir. Envuelve la condición CASE en una SUM para devolver el total de partidos jugados en cada temporada. Agrupa la consulta por el alias del «country name».

```
SELECT 
	c.name AS country,
-- Suma el total de ocasiones en cada temporada donde ganó el equipo local
    	SUM(CASE WHEN m.season = '2012/2013' AND m.home_goal > m.away_goal THEN 1 ELSE 0 END) AS matches_2012_2013,
	SUM(CASE WHEN m.season = '2013/2014' AND m.home_goal > m.away_goal THEN 1 ELSE 0 END) AS matches_2013_2014,
	SUM(CASE WHEN m.season = '2014/2015' AND m.home_goal > m.away_goal THEN 1 ELSE 0 END) AS matches_2014_2015
FROM country AS c
LEFT JOIN match AS m
ON c.id = m.country_id
-- Group by country alias
GROUP BY country;
```

### Calcular el porcentaje con CASE y AVG

-- Crea 3 declaraciones CASE para «COUNT» el número total de victorias del equipo local, victorias del equipo visitante y empates, lo que te permitirá examinar el número total de registros.

```
SELECT 
    c.name AS country,
    -- Suma las victorias locales y visitantes, y los empates en cada pais
	COUNT(CASE WHEN m.home_goal > m.away_goal THEN m.id END) AS home_wins,
	COUNT(CASE WHEN m.home_goal < m.away_goal THEN m.id END) AS away_wins,
	COUNT(CASE WHEN m.home_goal = m.away_goal THEN m.id END) AS ties
FROM country AS c
LEFT JOIN matches AS m
ON c.id = m.country_id
GROUP BY country;
```

## 2️⃣ Subconsultas cortas y sencillas

Aprenderás acerca de las subconsultas de las cláusulas SELECT, FROM y WHERE. Comprenderás cuándo son necesarias las subconsultas para construir tu conjunto de datos y cuál es la mejor manera de incluirlas en tus consultas.

### Filtrado mediante subconsultas escalares

-- Calcula el triple de la media de goles marcados en casa y fuera de casa en todos los partidos. En el siguiente paso esto se convertira en tu subconsulta. Ten en cuenta que esta columna no tiene un alias, por lo que se llamara "column" en los resultados.

```
-- Selecciona la media de goles "home" y "away", multiplicada por 3
SELECT
3 * AVG(home_goal + away_goal)
FROM matches_2013_2014;
```

### Filtrado mediante una subconsulta con una lista

-- Crea una subconsulta en la clausula WHERE que recupere todos los valores unicos de hometean_ID en la tabla match. Selecciona team_long_name y team_short_nane de la tabla team. Excluye todos los valores de la subconsulta de la consulta principal.

```
SELECT
-- Selecciona team Long y short names
team_Long_name,
team_short_name
FROM team
-- Excluye todos los valores de la subconsulta
WHERE team_api_id NOT IN
(SELECT DISTINCT hometeam_id FROM match);
```

### Filtrado con condiciones de subconsulta mas complejas

-- Crea una subconsulta en la clausula WHERE que recupere todos los valores hometeam_ID de la tabla match con una puntuacion home_goal superior o igual a 8. Selecciona team_long_name y team_short_nane de la tabla team. Incluye todos los valores de la subconsulta en la consulta principal.


```
SELECT
-- Selecciona team Long y short names
	team_Long_name,
	team_short_name
	FROM team
-- Filtra por equipos con 8 o más goles locales
WHERE team_api_id IN
	(SELECT hometeam_ID
	FROM match
	WHERE home_goal >= 8 );
```

### Subconsultas con FROM 

-- Crea la subconsulta que se utilizará en el siguiente paso, que selecciona el ID del país y se corresponde con el ID ( id ) de la tabla match. Filtra la consulta para ver los partidos con más o menos 10 goles.

```
SELECT
-- Selecciona country ID y match ID
	country_id,
	id
FROM match
-- Filtra por partidos con 10 o mas goles en total
WHERE (home_goal + away_goal) >= 10;
```

-- Completa la subconsulta dentro de la clausula FROM . Selecciona el nombre del pais en la tabla de paises, junto con la fecha, los goles en casa, los goles fuera de casa y el total de goles de la tabla de
partidos. Crea una columna en la subconsulta que afada los goles en casa y fuera de casa, llamada total_goals, esta se usara para filtrar la consulta principal. Selecciona el pais, la fecha, los goles en casa y los goles fuera de casa en la consulta principal. Filtra la consulta principal para ver los partidos con 10 o mas goles en total.


```
SELECT
-- Selecciona country, date, home, y "away goals" de la subconsulta
	country,
	date,
	home_goal,
	away_goal
FROM
-- Selecciona country name, date, home_goal, away_goal, y goles totales en la subconsulta
	(SELECT c.name AS country,
		m.date,
		m.home_goal,
		m.away_goal,
		(m.home_goal + m.away_goal) AS total_goals
	FROM match AS m
	LEFT JOIN country AS c
	ON m.country_id = c.id) AS subq
-- Filtra por "total goals" marcados en la consulta principal
WHERE total_goals >= 10 ;
```

### Subconsultas en SELECT

-- En la subconsulta, selecciona el promedio total de goles sumando home_goal y away_goal. Filtra los resultados para que solo se calcule la media de goles de la temporada 2013/2014. En la consulta principal, selecciona el promedio total de goles sumando home_goal y away_goal. Esto calcula el promedio de goles de cada liga. Filtra los resultados de la consulta principal del mismo modo que filtraste la subconsulta. Agrupa la consulta por el nombre de la liga.

```
SELECT
	L.name AS League,
-- Selecciona y redondea los goles totales de la liga
	ROUND (AVG(m.home_goal + m.away_goal), 2) AS avg_goals,
-- Selecciona y redondea la media de los goles totales de La Liga
	(SELECT ROUND(AVG(home_goal + away_goal), 2)
	FROM match
	WHERE season = '2013/2014') AS overall_avg
FROM League AS L
LEFT JOIN match AS m
ON L.country_id = m.country_id
-- Filtra por la temporada 2013/2014
WHERE season = '2013/2014'
GROUP BY L.name;
```

#### Subconsultas en Select para hacer calculos

-- Selecciona el promedio de goles marcados en un partido para cada liga en la consulta principal. Selecciona el promedio de goles marcados en un partido en general para la temporada 2013/2014 en
la subconsulta. Resta la subconsulta del numero medio de goles calculado para cada liga. Filtra la consulta principal para que solo se incluyan los partidos de la temporada 2013/2014.

```
SELECT
-- Selecciona el nombre de la liga y el promedio de goles marcados
	name AS League,
	ROUND (AVG (m.home_goal + m.away_goal), 2) AS avg_goals,
-- Substrae el promedio de match del promedio de la liga
	ROUND (AVG (m. home_goal + m.away_goal) -
	(SELECT AVG(home_goal + away_goal)
	FROM match
	WHERE season = '2013/2014'),2) AS diff
FROM League AS L
LEFT JOIN match AS m
ON L.country_id = m.country_id
-- Incluye solo resultados de 2013/2014
WHERE season = '2013/2014'
GROUP BY L.name;
```

-- Extrae el numero promedio de goles del equipo local y visitante en dos subconsultas SELECT. Calcula el promedio de goles en casa y fuera de casa para la fase especifica en la consulta principal. Filtra tanto las subconsultas como la consulta principal para que solo se incluyan los datos de la temporada 2012/2013. Agrupa la consulta por la columna m.stage.

```
SELECT
-- Selecciona stage y promedio de goles para cada stage
	m. stage,
	ROUND (AVG(m.home_goal + m.away_goal), 2) AS avg_goals,
-- Selecciona la media de goles totales para la temporada 2012/2013
	ROUND ( (SELECT AVG(home_goal + away_goal)
	FROM match
	WHERE season = '2012/2013'), 2) AS overalL
	FROM match AS m
-- Filtra por la temporada 2012/2013
WHERE season = '2012/2013'
-- Group by stage
GROUP BY m.stage;
```

### Subconsulta en FROM

Calcula el promedio de goles en casa y el promedio de goles fuera de la tabla de partidos para cada fase en la cláusulas FROM de la subconsulta. Añade una subconsulta a la clausula WHERE que calcula el promedio general de goles en casa. Filtra la consulta principal para ver las etapas en las que la media de goles en casa es superior a la media general. Seleccione las columnas stage y avg_goals de la subconsulta s en la consulta principal.

```
SELECT
-- Selecciona stage y goles promedios de la subconsulta
	stage,
	ROUND (s . avg_goals, 2) AS avg_goals
FROM
-- Selecciona stage y goles promedios en 2012/2013
	(SELECT
	stage,
	AVG(home_goal + away_goal) AS avg_goals
	FROM match
	WHERE season = '2012/2013'
	GROUP BY stage) AS s
WHERE
-- Filtra la consulta principal usando la subconsulta
	s.avg_goals > (SELECT AVG(home_goal + away_goal)
			FROM match WHERE season = '2012/2013');
```

## 3️⃣ Consultas correlacionadas, consultas anidadas y expresiones de tablas comunes

Aprenderás a usar subconsultas anidadas y correlacionadas para extraer datos más complejos de una base de datos relacional. También aprenderás acerca de las expresiones de tabla comunes y cómo construir consultas mejor usando varias expresiones de tabla comunes.


### Subconsultas correlacionadas basicas

Las subconsultas correlacionadas son subconsultas que hacen referencia a una o mas columnas de la consulta principal. Las subconsultas correlacionadas dependen de la informacion en la consulta principal para ejecutarse y, por lo tanto, no pueden ejecutarse por si solas. 

-- Selecciona las columnas country_id, date , home_goal y away_goal en la consulta principal. Completa el valor de AVG en la subconsulta. Completa las referencias de la columna de subconsulta, para que country_id coincida en la consulta principal y en la subconsulta.

```
SELECT
-- Selecciona country ID, date, home, y away goals de match
	main.country_id,
	main.date,
	main.home_goal,
	main.away_goal
FROM match AS main
WHERE
-- Filtra la consulta principal con la subconsulta
	(home_goal + away_goal) >
		(SELECT AVG((sub.home_goal + sub.away_goal) * 3)
		FROM match AS sub
-- Haz join entre la consulta y la subconsulta en WHERE
		WHERE main.country_id = sub.country_id);
```

### Subconsulta correlacionada con multiples condiciones

-- Selecciona las columnas country_id, date , home_goal y away_goal en la consulta principal. Completa la cubsconsulta: Selecciona los partidos con mayor número de goles totales. Haz coincidir la subconsulta con la consulta principal utilizando country_id y season. Rellena el operador logico correcto para que los goles totales sean iguales a los goles maximos registrados en la subconsulta.

```
SELECT
-- Selecciona country ID, date, home, y away goals de match
	main.country_id,
	main.date,
	main.home_goal,
	main.away_goal
FROM match AS main
WHERE
-- Filtra por matches con el mayor número de goles marcados
	(home_goal + away_goal) =
		(SELECT MAX(sub.home_goal + sub.away_goal)
		FROM match AS sub
		WHERE main.country_id = sub.country_id
			AND main.season = sub.season);
```

### Subconsultas simples anidadas

Las subconsultas anidadas pueden ser simples o correlacionadas.

Al igual que una subconsulta no anidada, los componentes de una subconsulta anidada se pueden ejecutar independientemente de la consulta externa, mientras que una subconsulta correlacionada requiere que la subconsulta externa e interna se ejecuten y produzcan resultados.

-- Completa la consulta principal para seleccionar la temporada y el maximo total de goles en un partido para cada temporada. Nombra a este max_goals. Completa la primera subconsulta simple para seleccionar el maximo total de goles en un partido en todas las temporadas. Nombra a este overall_max_goals. Completa la subconsulta anidada para seleccionar el maximo total de goles en un partido jugado en julio a lo largo de todas las temporadas. Selecciona el maximo total de goles en la subconsulta exterior. Nombra a esta subconsulta july_max_goals.

```
SELECT
-- Selecciona la season y max de goles marcados en un partido
	season,
	MAX(home_goal + away_goal) AS max_goals,
-- Selecciona el max de goles marcados en cualquier partido
	(SELECT MAX(home_goal + away_goal) FROM match) AS overall_max_goals,
-- Selecciona el max de goles marcados en cualquier partido en Julio
	(SELECT MAX(home_goal + away_goal)
	FROM match
	WHERE EXTRACT (MONTH FROM date) = 07) AS july_max_goals
FROM match
GROUP BY season;
```

### Anidar una subconsulta en FROM

-- Genera una lista de partidos donde al menos un equipo marco 5 o mas goles.

```
-- Selecciona matches donde un equipo marco 5+ goles
SELECT
	country_id,
	season,
	id
FROM match
WHERE home_goal >=5 OR away_goal >=5;
```

### Expresiones de tabla común

### Limpia con CTEs

-- Completa la sintaxis para declarar tu CTE. Selecciona los country_id y emparejalos con id de la tabla match en tu CTE. Haz un «left join» del CTE a la tabla de «league» usando country_id.

```
-- Crea tu CTE
WITH match_list AS (
	SELECT
		country_id,
		id
	FROM match
	WHERE (home_goal + away_goal) >= 10)
-- Selecciona league y conteo de matches del CTE
SELECT
	L.name AS League,
	COUNT (match_list.id) AS matches
FROM League AS L
-- Haz join del CTE a la tabla league
LEFT JOIN match_list ON L.id = match_list.country_id
GROUP BY L.name;
```

## 4️⃣ Funciones de ventana

Aprenderás sobre las funciones de ventana y cómo transferir funciones agregadas a lo largo de un conjunto de datos. También aprenderás a calcular los totales acumulados y los promedios particionados.

```

```
