# GROUP BY and HAVING

## GROUP BY

* `GROUP BY` clause divide the rows returned from `SELECT` statement into groups
* For each group, you can apply aggregate functions like `COUNT`, `SUM`, `MIN`, `MAX` etc.

### Syntax

```sql
SELECT column1,
       AGGREGATE_FUNCTION(column2)
FROM tablename
GROUP BY column1;

```

### Group BY

Group the data in column and pass it to aggregate function

### Group BY with count

```sql
SELECT 
    movie_lang,
    COUNT(movie_lang) as count
FROM
    movies
GROUP BY
    movie_lang
ORDER BY 
    count ASC;

 movie_lang | count 
------------+-------
 Swedish    |     1
 German     |     1
 Korean     |     1
 Spanish    |     1
 Portuguese |     2
 Japanese   |     4
 Chinese    |     5
 English    |    38
```

### Group BY with SUM

```sql
SELECT 
    age_certificate,
    SUM(movie_length) 
FROM 
    movies 
GROUP BY 
    age_certificate;
    
 age_certificate | sum  
-----------------+------
 PG              | 1462
 15              | 2184
 12              | 1425
 18              |  994
 U               |  620
```

### Group BY with MIN, MAX

```sql
SELECT movie_lang,
       MIN(movie_length),
       MAX(movie_length)
FROM movies
GROUP BY movie_lang

 movie_lang | min | max 
------------+-----+-----
 Portuguese | 140 | 145
 German     | 165 | 165
 Chinese    |  99 | 139
 English    |  87 | 168
 Swedish    | 128 | 128
 Spanish    |  98 |  98
 Korean     | 130 | 130
 Japanese   | 107 | 120

SELECT 
    movie_lang,
    MIN(movie_length),
    MAX(movie_length)
FROM
    movies
GROUP BY
    movie_lang
ORDER BY MAX(movie_length) DESC;

 movie_lang | min | max 
------------+-----+-----
 English    |  87 | 168
 German     | 165 | 165
 Portuguese | 140 | 145
 Chinese    |  99 | 139
 Korean     | 130 | 130
 Swedish    | 128 | 128
 Japanese   | 107 | 120
 Spanish    |  98 |  98

```

## HAVING

* We use `HAVING` clause to specify a search condition for a **group or an aggregate**
* The **`HAVING` clause is often used with the `GROUP BY`** clause to filter rows based on filter condition
* cannot use column alias with having clause because it is evaluated before the `SELECT` statement

```sql
SELECT 
    column1,
    AGGREGATE_FUNCTION(column2),
FROM tablename
GROUP BY column1
HAVING 
    condition;
```

* `HAVING AGGREGATE_FUNCTION(column2) = value`
* `HAVING AGGREGATE_FUNCTION(column2) >= value`

```sql
SELECT 
    movie_lang, 
    SUM(movie_length) 
FROM 
    movies 
GROUP BY 
    movie_lang 
HAVING SUM(movie_length) > 200 
ORDER BY SUM(movie_length);

 movie_lang | sum  
------------+------
 Portuguese |  285
 Japanese   |  446
 Chinese    |  609
 English    | 4824

```

### HAVING vs WHERE

* `HAVING` works on result group
* **`WHERE` works on `SELECT` columns and not on the result group**

```sql
SELECT
    movie_lang,
    SUM(movie_length)
FROM 
    movies
GROUP BY movie_lang
ORDER BY 2 DESC;

 movie_lang | sum  
------------+------
 English    | 4824
 Chinese    |  609
 Japanese   |  446
 Portuguese |  285
 German     |  165
 Korean     |  130
 Swedish    |  128
 Spanish    |   98
```

