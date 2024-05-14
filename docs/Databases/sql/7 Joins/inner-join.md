# Inner Join

## Basic

![image](./assets/inner-join.png)

**Remember : All common column defined at ON must match values on both tables**

```sql
-- syntax

SELECT
 table_a.column1
 table_b.column2
FROM
 table_a
INNER JOIN table_b ON table1.column1 = table2.column2


SELECT mv.*,
       dir.*
FROM movies as mv
         INNER JOIN directors as dir
                    ON mv.director_id = dir.director_id
LIMIT 5;

-- director_id column will be repeated

 movie_id |       movie_name       | movie_length | movie_lang | release_date | age_certificate | director_id | director_id | first_name | last_name | date_of_birth | nationality 
----------+------------------------+--------------+------------+--------------+-----------------+-------------+-------------+------------+-----------+---------------+-------------
       20 | Let the Right One In   |          128 | Swedish    | 2008-10-24   | 15              |           1 |           1 | Tomas      | Alfredson | 1965-04-01    | Swedish
       46 | There Will Be Blood    |          168 | English    | 2007-12-26   | 15              |           2 |           2 | Paul       | Anderson  | 1970-06-26    | American
       40 | The Darjeeling Limited |          119 | English    | 2007-09-29   | PG              |           3 |           3 | Wes        | Anderson  | 1969-05-01    | American
       30 | Rushmore               |          104 | English    | 1998-11-12   | 12              |           3 |           3 | Wes        | Anderson  | 1969-05-01    | American
       15 | Grand Budapest Hotel   |          117 | English    | 2014-07-03   | PG              |           3 |           3 | Wes        | Anderson  | 1969-05-01    | American

```

### Selecting only required columns

```sql
SELECT movie_name,
       dir.first_name || ' ' || dir.last_name 
           as "Director Name",
       mv.movie_id,
       dir.director_id
FROM movies as mv
         INNER JOIN directors as dir 
             ON mv.director_id = dir.director_id
LIMIT 5;

       movie_name       |  Director Name  | movie_id | director_id 
------------------------+-----------------+----------+-------------
 Let the Right One In   | Tomas Alfredson |       20 |           1
 There Will Be Blood    | Paul Anderson   |       46 |           2
 The Darjeeling Limited | Wes Anderson    |       40 |           3
 Rushmore               | Wes Anderson    |       30 |           3
 Grand Budapest Hotel   | Wes Anderson    |       15 |           3

```

### with WHERE clause 

```sql
SELECT movie_name,
       mv.movie_lang,
       dir.first_name || ' ' || dir.last_name 
          as "Director Name",
       mv.movie_id,
       dir.director_id
FROM movies as mv
         INNER JOIN directors as dir
            ON mv.director_id = dir.director_id
WHERE mv.movie_lang = 'English' limit 5;

       movie_name       | movie_lang | Director Name  | movie_id | director_id 
------------------------+------------+----------------+----------+-------------
 There Will Be Blood    | English    | Paul Anderson  |       46 |           2
 The Darjeeling Limited | English    | Wes Anderson   |       40 |           3
 Rushmore               | English    | Wes Anderson   |       30 |           3
 Grand Budapest Hotel   | English    | Wes Anderson   |       15 |           3
 Submarine              | English    | Richard Ayoade |       38 |           4
```

## Inner Join with USING

* we use USING  only when joining tables have the SAME column name, rather then ON !

```sql
select
    table1.column1,
    table2.column1
from
    table1
INNER JOIN 
    table2 USING (column1);


select *
from movies
         INNER JOIN directors USING (director_id)
LIMIT 10;

 director_id | movie_id |     movie_name     | movie_length | movie_lang | release_date | age_certificate | first_name |  last_name   | date_of_birth | nationality 
-------------+----------+--------------------+--------------+------------+--------------+-----------------+------------+--------------+---------------+-------------
          13 |        1 | A Clockwork Orange |          112 | English    | 1972-02-02   | 18              | Stanley    | Kubrick      | 1928-07-26    | American
           9 |        2 | Apocalypse Now     |          168 | English    | 1979-08-15   | 15              | Francis    | Ford Coppola | 1939-04-07    | American
```

