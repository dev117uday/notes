# Aggregation

* `COUNT (column)`
* `SUM (column)`
* `MIN & MAX`
* `LEAST & GREATEST`
* `AVG`

## `count`

```sql
SELECT COUNT(DISTINCT (movie_lang))
from movies;

 count 
-------
     8

-- with where clause

SELECT COUNT(movie_lang)
FROM movies
where movie_lang = 'English';

 count 
-------
    38
```

## Sum

```sql
select sum(revenues_domestic)
from movies_revenues;

  sum   
--------
 5719.5

select SUM(revenues_domestic::numeric)
from movies_revenues
where revenues_domestic::numeric > 200;

  sum   
--------
 3425.6

SELECT SUM(DISTINCT revenues_domestic)
FROM movies_revenues;

  sum   
--------
 5708.4
```

## Min and Max

```sql
SELECT min(movie_length), MAX(movie_length)
FROM movies

 min | max 
-----+-----
  87 | 168
```

## Average, Greatest, Latest

```sql
SELECT GREATEST(10, 20, 30, 40), LEAST(10, 20, 30, 40);

 greatest | least
----------+-------
       40 |    10

SELECT GREATEST('A', 'B', 'C', 'D'), LEAST('A', 'B', 'C', 'D');

 greatest | least
----------+-------
 D        | A

SELECT GREATEST('A', 'B', 'C', 1);

-- ERROR:  invalid input syntax for type integer: "A"
-- LINE 1: SELECT GREATEST('A', 'B', 'C', 1);

SELECT AVG(movie_length)
FROM movies;

         avg          
----------------------
 126.1320754716981132
```

