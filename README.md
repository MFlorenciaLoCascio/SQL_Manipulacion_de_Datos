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
WHERE 	team_long_name = 'Bologna';
```


### COUNT usando CASE WHEN

-- Crea una condición CASE que identifica la id de partidos disputados en la temporada 2012/2013. Especifica que quieres que los valores ELSE sean NULL. Envuelve la condición CASE en una función COUNT y agrupa la consulta con el alias country.

```
SELECT 
	c.name AS country,
    -- Cuenta partidos de la temporada 2012/2013
	COUNT(CASE WHEN m.season = '2012/2013' 
         THEN m.id ELSE NULL END) AS matches_2012_2013
FROM country AS c
LEFT JOIN match AS m
ON c.id = m.country_id
-- Group by country name alias
GROUP BY country;
```



## 2️⃣ Subconsultas cortas y sencillas

Aprenderás acerca de las subconsultas de las cláusulas SELECT, FROM y WHERE. Comprenderás cuándo son necesarias las subconsultas para construir tu conjunto de datos y cuál es la mejor manera de incluirlas en tus consultas.

```

```

## 3️⃣ Consultas correlacionadas, consultas anidadas y expresiones de tablas comunes

Aprenderás a usar subconsultas anidadas y correlacionadas para extraer datos más complejos de una base de datos relacional. También aprenderás acerca de las expresiones de tabla comunes y cómo construir consultas mejor usando varias expresiones de tabla comunes.

```

```

## 4️⃣ Funciones de ventana

Aprenderás sobre las funciones de ventana y cómo transferir funciones agregadas a lo largo de un conjunto de datos. También aprenderás a calcular los totales acumulados y los promedios particionados.

```

```
