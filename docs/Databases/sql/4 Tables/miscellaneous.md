---
description: bodmas 3000
---

# OPERATORS

## Logical

```sql
SELECT 1=1, 1<1, 1>1, 1<=1, 1>=1;

 ?column? | ?column? | ?column? | ?column? | ?column? 
----------+----------+----------+----------+----------
 t        | f        | f        | t        | t

SELECT 1<>1; 
 ?column? 
----------
 f
 
SELECT 1 = 1 or 1 = 2 ;
 ?column? 
----------
 t

select 1 / 0;
-- ERROR:  division by zero
```

## Pattern Matching

```sql
SELECT * FROM actors WHERE last_name LIKE '%son%';

  first_name |  last_name  | gender |    date_of_birth    
------------+-------------+--------+---------------------
 Woody      | Harrelson   | M      | 1961-07-23
 Samuel     | Jackson     | M      | 1948-12-21
 Lina       | Leandersson | F      | 1995-09-27
 Jack       | Nicholson   | M      | 1937-04-22
 Mykelti    | Williamson  | M      | 1957-03-04
 Luke       | Wilson      | M      | 1971-09-21
 Owen       | Wilson      | M      | 1968-11-18
 Patrick    | Wilson      | M      | 1973-07-03

-- ILIKE
-- to ignore the case
SELECT * FROM actors WHERE last_name ILIKE '__i%'; 

 first_name | last_name | gender |    date_of_birth    
------------+-----------+--------+---------------------
 Hiroki     | Doi       | M      | 1999-08-10 
 Alec       | Guiness   | M      | 1914-04-02 
 Rumi       | Hiiragi   | F      | 1987-08-01 
 Miyu       | Irino     | M      | 1988-02-19 
 Keira      | Knightley | F      | 1985-03-26 
 Vivien     | Leigh     | F      | 1913-11-05 
 Yasmin     | Paige     | F      | 1991-06-24 
 Tilda      | Swinton   | F      | 1960-11-05 
 Robin      | Wright    | F      | 1966-04-08 

select 'hello' like 'hello';
select 'hello' like 'h%';
select 'hello' like '%e%';
select 'hello' like '%lo';
select 'hello' like '_ello';
select 'hello' like '__llo';
select 'hello' like '%ll_';

 ?column? | ?column? | ?column? | ?column? | ?column? | ?column? | ?column? 
----------+----------+----------+----------+----------+----------+----------
 t        | t        | t        | t        | t        | t        | t


-- like with length of characters
select * from table where name like '____';
```

### Tips

* When using `AND` and `OR` in sample `SQL` statement, use brackets to differentiate between statements
* `AND`  operator is processed before OR operator
* `SQL` treats `AND` operator like multiplication and `OR` like divide

### Fetch

```sql
-- OFFSET start { ROW | ROWS }
-- FETCH { FIRST | NEXT } { ROW_COUNT } { ROWS|ROW } ONLY

SELECT
    *
FROM movies
fetch first row only ;

 movie_id |     movie_name     | movie_length | movie_lang | release_date | age_certificate | director_id 
----------+--------------------+--------------+------------+--------------+-----------------+-------------
        1 | A Clockwork Orange |          112 | English    | 1972-02-02   | 18              |          13


SELECT
    *
FROM movies
offset 3
fetch next 10 row only ;

 movie_id |          movie_name           | movie_length | movie_lang | release_date | age_certificate | director_id 
----------+-------------------------------+--------------+------------+--------------+-----------------+-------------
        4 | Blade Runner                  |          121 | English    | 1982-06-25   | 15              |          27
        5 | Chungking Express             |          113 | Chinese    | 1996-08-03   | 15              |          35
        6 | City of God                   |          145 | Portuguese | 2003-01-17   | 18              |          20
        7 | City of Men                   |          140 | Portuguese | 2008-02-29   | 15              |          22
        8 | Cold Fish                     |          108 | Japanese   | 2010-09-12   | 18              |          30
        9 | Crouching Tiger Hidden Dragon |          139 | Chinese    | 2000-07-06   | 12              |          15
       10 | Eyes Wide Shut                |          130 | English    | 1999-07-16   | 18              |          13
       11 | Forrest Gump                  |          119 | English    | 1994-07-06   | PG              |          36
       12 | Gladiator                     |          165 | English    | 2000-05-05   | 15              |          27
       13 | Gone with the Wind            |          123 | English    | 1939-12-15   | PG              |           8

```

## IS NULL or IS NOT NULL

```sql
select * from actors where date_of_birth is null;

 first_name | last_name | gender | date_of_birth 
------------+-----------+--------+---------------
 Xian       | Gao       | M      | 
```

