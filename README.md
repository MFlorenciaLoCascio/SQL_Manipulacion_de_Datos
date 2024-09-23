# Algunos de los ejercicios del curso de Manipulación de Datos en SQL

Puedes acceder al curso  [aquí](https://app.datacamp.com/learn/courses/data-manipulation-in-sql), tiene una duración de 4 horas, 15 videos y 55 ejercicios. 👩‍💻

Certificado  [aquí](https://www.datacamp.com/completed/statement-of-accomplishment/course/b562e32da02cd9acdbe3bbfafc45819ffc25c1bd)

🔖 Puede seguir la ruta de los cursos en el siguiente orden: 

1- [SQL_Intermedio](https://github.com/MFlorenciaLoCascio/SQL_Intermedio)

2- [Unión de Datos](https://github.com/MFlorenciaLoCascio/SQL_Union_de_Datos) 

3- [Manipulación de Datos](https://github.com/MFlorenciaLoCascio/SQL_Manipulacion_de_Datos) Aquí esta ahora

4- [Resumen de estadísticas y funciones de ventana](https://github.com/MFlorenciaLoCascio/SQL_Funciones_de_Ventana)

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

### Organizacion con CTEs

-- Declara tu CTE, donde creas una lista de todos los partidos con el nombre de la liga. Selecciona la liga, la fecha, la casa y los goles fuera de casa del CTE. Filtra la consulta principal para partidos con 10 o más goles.

```
--Crea tu CTE
WITH match_list AS (
Selecciona league, date, home, y away goals
	SELECT
		L.name AS League,
		m.date,
		m.home_goal,
		m.away_goal,
			(m.home_goal + m.away_goal) AS total_goals
	FROM match AS m
	LEFT JOIN League as L ON m.country_id = L.id)
-- Selecciona league, date, home, y away goals del CTE
SELECT League, date, home_goal, away_goal
FROM match_list
-- Filtra por total goals
WHERE total_goals >=10 ;
```

### CTE con subconsultas anidadas

-- Declara un CTE que calcule el total de goles de los partidos de agosto de la temporada 2013/2014. Haz un «left join» del CTE en la tabla de liga usando country_id del CTE match_list. Filtra la lista en la subconsulta interna para seleccionar solo los partidos en agosto de la temporada 2013/2014.

```
-- Crea tu CTE
WITH match_list AS (
	SELECT
		country_id,
		(home_goal + away_goal) AS goals
	FROM match
-- Crea una lista de match IDs para filtrar datos en el CTE
	WHERE id IN (
		SELECT id
		FROM match
		WHERE season = '2013/2014' AND EXTRACT(MONTH FROM date) = '8'))
-- Selecciona el nombre de la liga y promedio de goles en el CTE
SELECT
	L.name,
	avg (goals)
FROM League AS L
-- Haz join del CTE en la tabla de la liga
LEFT JOIN match_list ON L.id = match_list.country_id
GROUP BY L.name;
```
-- Crea una consulta haciendo un «left join de match a tean con el fin de obtener la identidad del equipo local. Esta se convierte en la subconsulta en el siguiente paso.

```
SELECT
	m.id.
	t.team_Long_name AS hometeam
-- Haz left join de team con match
FROM match AS m
LEFT JOIN team as t
ON m.hometeam_id = team_api_id;
```

-- Usando una subconsulta correlacionada en la sentencia SELECT , haz coincidir la columna team_api_id de team a la columna hometeam_id de match.

```
SELECT
	m. date
	(SELECT team_Long_name
	FROM team AS t
-- Conecta team a la tabla match
	WHERE t.team_api_id = m.hometeam_id) AS hometeam
FROM match AS m;
```
-- Selecciona id de match y team_long_name de team . Une estas dos tablas con hometeam_id de match y team_api_id de team.

```
SELECT
-- Selecciona match id y team long name
	m.id
	t.team_Long_name AS hometeam
FROM match AS m
-- Haz join de team a match usando team_api_id y hometeam_id
LEFT JOIN team AS t
ON m.hometeam_id = t.team_api_id;
```

## 4️⃣ Funciones de ventana

Aprenderás sobre las funciones de ventana y cómo transferir funciones agregadas a lo largo de un conjunto de datos. También aprenderás a calcular los totales acumulados y los promedios particionados.

### OVER: 

La clausula OVER() te permite transferir una funcion agregada a un conjunto de datos, de forma similar a las subconsultas en SELECT . La clausula OVER() ofrece ventajas importantes con respecto a las subconsultas de select, es decir, las consultas se ejecutarán más rápido y la cláusula OVER() tiene una amplia gama de funciones y clausulas adicionales que puede incluir.

-- Selecciona el identificador del partido (ID), el nombre del pais, la temporada, los goles en casa y fuera de las tablas match y country. Completa la consulta que calcula el número medio de goles marcados en general y, a continuación, incluye el valor agregado en cada fila mediante una funcion de ventana.

```
SELECT
-- Selecciona el id, country name, season, home, y away goals
	m.id.
	c.name AS country,
	m. season,
	m. home_goal,
	m.away_goal,
-- Usa una ventana para incluir el promedio agregado en cada fila
	AVG(m.home_goal + m.away_goal) OVER() AS overall_avg
FROM match AS m
LEFT JOIN country AS c ON m.country_id = c.id;
```
### RANK:

Las funciones de ventana te permiten crear un rango ( RANK ) de la informacion de acuerdo con cualquier variable que desees utilizar para ordenar los datos. Al configurar esto, tendras que especificar que columna/calculo quieres usar para calcular tu rango. Esto se hace mediante la inclusión de una cláusula ORDER BY dentro de la cláusula OVER().

-- Selecciona el nombre de la liga y el promedio total de goles marcados usando League y match. Completa la funcion de ventana para que calcule la clasificacion de la media de goles marcados en todas las ligas de la base de datos. Ordena la clasificacion segun el promedio total de goles marcados en casa y fuera de casa.

```
SELECT
--Selecciona League name y average goals marcados
	L.name AS League,
	AVG(m.home_goal + m.away_goal) AS avg_goals,
-- Crea un rank para cada liga basado en los goles promedios
	RANK() OVER( ORDER BY AVG(m.home_goal + m. away_goal)) AS League_rank
FROM League AS L
LEFT JOIN match AS m
ON L.id = m.country_id
WHERE m.season = '2011/2012'
GROUP BY L.name
-- Ordena la consulta por el rank que has creado
ORDER BY League_rank;
```

-- Completa las mismas partes de la consulta que en el ejercicio anterior. Completa la funcion de ventana para clasificar cada liga del promedio de goles anotados del más alto al más bajo. Ordena la consulta principal por el rango que acabas de crear.

```
SELECT
-- Selecciona League name y average goals marcados
	L.name AS League,
	AVG(m.home_goal + m.away_goal) AS avg_goals,
-- Crea un rank descendiente para cada liga basado en los goles promedios
	RANK() OVER(ORDER BY AVG(m. home_goal + m.away_goal) DESC) AS League_rank
FROM League AS L
LEFT JOIN match AS m
ON L.id = m.country_id
WHERE m.season = '2011/2012'
GROUP BY L.name
-- Ordena la consulta por el rank que has creado
ORDER BY League_rank;
```

### PARTITION BY una columna

La clausula PARTITION BY te permite calcular "ventanas" independientes en funcion de las columnas en las que desees dividir los resultados. Por ejemplo, puedes crear una sola columna que calcule un promedio general de goles marcados en cada temporada.

-- Completa las dos funciones de ventana que calculan los promedios de goles en casa y fuera de casa. Divide las funciones de ventana por temporada para calcular promedios separados para cada temporada. Filtra la consulta para incluir solo los partidos jugados por el Legia Warszawa, id = 8673.

```
SELECT
	date,
	season,
	home_goal,
	away_goal,
	CASE WHEN hometeam_id = 8673 THEN 'home'
		ELSE 'away' END AS warsaw_location,
-- Calcula los goles marcados promedio particionados por la temporada (season)
	AVG(home_goal) OVER(partition by season) AS season_homeavg,
	AVG(away_goal) OVER(partition by season) AS season_awayavg
FROM match
-- Filtra el data set solo para los partidos del Legia Warszawa
WHERE
	hometeam_id = 8673
	OR awayteam_id = 8673
ORDER BY (home_goal + away_goal) DESC;
```

### PARTITION BY múltiples columnas

La cláusula PARTITION BY se puede utilizar para desglosar los promedios de las ventanas por varios puntos de datos (columnas). ¡Incluso puedes calcular la informacion que deseas usar para particionar tus datos! Por ejemplo, puedes calcular el promedio de goles marcados por temporada y por país, o por año natural (tomado de la columna de fecha).

-- Construye dos funciones de ventana que dividan la media de goles en casa y fuera de casa por temporada y mes. Filtra el conjunto de datos por el ID de equipo ( 8673 ) del Legia Warszawa para que el calculo de la ventana solo incluya los partidos en los que esten involucrados.

```
SELECT
	date,
	season,
	home_goal,
	away_goal,
CASE WHEN hometeam_id = 8673 THEN 'home'
	ELSE 'away' END AS warsaw_location,
-- Calcula los goles promedio particionando por temporada y mes
AVG(home_goal) OVER(PARTITION BY season,
	EXTRACT (MONTH FROM date)) AS season_mo_home,
AVG(away_goal) OVER(PARTITION BY season,
	EXTRACT (MONTH FROM date)) AS season_mo_away
FROM match
WHERE
	hometeam_id = 8673
	OR awayteam_id = 8673
ORDER BY (home_goal + away_goal) DESC;
```

### Ventanas deslizantes, funciones PRECEDING, FOLLOWING , y CURRENT ROW

### Desliza hacia la izquierda

Las ventanas deslizantes permiten crear calculos continuos entre dos puntos de una ventana mediante funciones como PRECEDING, FOLLOWING , y CURRENT ROW . Puedes calcular los recuentos continuos, las sumas, los promedios y otras funciones de agregacion entre los dos puntos que se especifiquen en el conjunto de datos.

-- Evalua el total acumulado de goles en casa marcados por el FC Utrecht. Evalua la media acumulada de goles marcados en casa. Ordena tanto la media acumulada como el total acumulado por date. 

```
SELECT
	date
	home_goal,
	away_goal,
-- Crea un running total de goles en casa
	SUM(home_goal) OVER (ORDER BY date
		ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running_total,
-- Crea una media acumulada de goles en casa
	AVG(home_goal) OVER (ORDER BY date
	ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running_avg
FROM match
WHERE
hometeam_id = 9908 -- ID del FC Utrecht
```

### Desliza hacia la derecha

-- Evalua el total acumulado de goles en casa marcados por el FC Utrecht. Evalua la media acumulada de goles marcados en casa. Ordena tanto la media acumulada como el total acumulado por date, orden descendente.

```
SELECT
	date
	home_goal,
	away_goal,
-- Crea un total acumulado de goles en casa, ordenado por fecha descendente
	SUM(home_goal) OVER (ORDER BY date DESC
		ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) AS running_total,
-- Crea una media acumulada de goles en casa, ordenada por fecha descendente
	AVG(home_goal) OVER (ORDER BY date DESC
		ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) AS running_avg
FROM match
WHERE
	awayteam_id = 9908
		AND season = '2011/2012';
```

-- Crea una condicion CASE que identifique cada partido como una victoria, una derrota o un empate para el Manchester United. Completa los operadores logicos de cada clausula WHEN de la condicion CASE (igual a, mayor que, menor que). Une las mesas con el ID del equipo local de match , y team_api_id de team. Filtra la consulta para incluir solo los partidos de la temporada 2014/2015 en los que el Manchester United fue el equipo local.

```
SELECT
	m.id
	t.team_Long_name,
-- Identifica partidos como victorias, derrotas o empates
	CASE WHEN m. home_goal > m.away_goal THEN 'MU Win'
		WHEN m.home_goal < m.away_goal THEN 'MU Loss'
		ELSE 'Tie' END AS outcome
FROM match AS m
-- Haz left join de team con home team ID y team API id
LEFT JOIN team AS t
ON m.hometeam_id = t.team_api_id
WHERE
-- Filtra por 2014/2015 y Manchester United como equipo local
	season = '2014/2015'
	AND t.team_long_name = 'Manchester United';
```

-- Completa la sintaxis de la condicion CASE. Completa los operadores lógicos que identifican cada partido como una victoria, una derrota o un empate para el Manchester United. Une la tabla utilizando awayteam_id y team_api_id.

```
SELECT
	m.id
	t.team_Long_name,
-- Identifica partidos como victorias, derrotas o empates
	CASE WHEN m.home_goal > m.away_goal THEN 'MU Loss'
		WHEN m.home_goal < m.away_goal THEN 'MU Win'
		ELSE 'Tie' END AS outcome
-- Haz join entre las tablas match y team
FROM match AS m
LEFT JOIN team AS t
ON m.awayteam_id = t.team_api_id
WHERE
-- Filtra por 2014/2015 y Manchester United como equipo visitante
	season = '2014/2015
	AND t.team_long_name = 'Manchester United';
```

-- 
