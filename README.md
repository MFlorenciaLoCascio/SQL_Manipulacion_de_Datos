# Algunos de los ejercicios del curso de Manipulación de Datos en SQL

Puedes acceder al curso  [aquí](https://app.datacamp.com/learn/courses/data-manipulation-in-sql), tiene una duración de 4 horas, 15 videos y 55 ejercicios. 👩‍💻

Certificado  [aquí](https://www.datacamp.com/completed/statement-of-accomplishment/course/b562e32da02cd9acdbe3bbfafc45819ffc25c1bd)

## Descripción del curso:

Aprenderás varias funciones clave necesarias para organizar, filtrar y categorizar información en una base de datos relacional. El uso robusto de «CASE statements», subconsultas y «window functions» (funciones de ventana), todo mientras descubres algunos datos interesantes sobre el fútbol utilizando la base de datos europea de fútbol.

## 1️⃣ Tomaremos el «CASE»

Aprenderás a utilizar la instrucción CASE WHEN para crear variables categóricas, agregar datos en una sola columna con múltiples condiciones de filtrado y calcular recuentos y porcentajes.

-- Selecciona el nombre largo del equipo y la ID de API de la tabla teams_germany. Filtra la consulta para FC Schalke 04 y FC Bayern Munich usando IN, dándole la team_api_IDs necesaria para el siguiente paso.

```
SELECT
	-- Selecciona el "team long name" y "team API id"
	team_long_name, team_api_id
	
FROM teams_germany
-- Incluye solo FC Schalke 04 y FC Bayern Munich
WHERE team_long_name IN ('FC Schalke 04', 'FC Bayern Munich');
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
