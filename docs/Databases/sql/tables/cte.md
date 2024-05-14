# Common Table Expression

* `CTE` is a temporary result take from a SQL statement
* A second approach to create temporary tables for query data instead of using sub queries in a `FROM` clause&#x20;
* CTE's are a good alternative to sub queries.
* `CTE` can be referenced multiple times in multiple places in query statement
* Lifetime of `CTE` is equal to the **lifetime of a query.**
* Types of `CTE`
  * Materialized
  * Not materialized

## Syntax

```sql
with cte_table ( column_list ) as (
    cte_query_definition
)

with num as (
    select *
    from generate_series(1, 5) as id
)
select *
from num;

 id 
----
  1
  2
  3
  4
  5
```

## CTE with Join

```sql

with cte_director as (
    select movie_id, movie_name, d.director_id, first_name
    from movies
             inner join directors d on d.director_id = movies.director_id
)
select *
from cte_director
limit 5;

 movie_id |       movie_name       | director_id | first_name 
----------+------------------------+-------------+------------
       20 | Let the Right One In   |           1 | Tomas
       46 | There Will Be Blood    |           2 | Paul
       40 | The Darjeeling Limited |           3 | Wes
       30 | Rushmore               |           3 | Wes
       15 | Grand Budapest Hotel   |           3 | Wes
```

## CTE with CASE

```sql
WITH cte_film AS (
    SELECT movie_name,
           movie_length
                    title,
           (CASE
                WHEN movie_length < 100 THEN 'Short'
                WHEN movie_length < 120 THEN 'Medium'
                ELSE 'Long'
               END) length
    FROM movies
)
SELECT *
FROM cte_film
WHERE length = 'Long'
ORDER BY title
limit 5;

     movie_name     | title | length 
--------------------+-------+--------
 The Wizard of Oz   |   120 | Long
 Spirited Away      |   120 | Long
 Top Gun            |   121 | Long
 Leon               |   123 | Long
 Gone with the Wind |   123 | Long

```

## Complex query example

```sql
WITH cte_movie_count AS
 (
     SELECT d.director_id,
            SUM(COALESCE(r.revenues_domestic, 0) 
            + COALESCE(r.revenues_international, 0)) 
                    AS total_revenues
     FROM directors d
              INNER JOIN movies mv 
                  ON mv.director_id = d.director_id
              INNER JOIN movies_revenues r 
                  ON r.movie_id = mv.movie_id
     GROUP BY d.director_id
 )
SELECT d.director_id,
       d.first_name,
       d.last_name,
       cte.total_revenues
FROM cte_movie_count cte
         INNER JOIN directors d 
         ON d.director_id = cte.director_id
LIMIT 5;
```

## CTE to perform DML

### Loading Sample Data

```sql
create table articles
(
    id      serial,
    article text
);

create table deleted_articles
(
    id      serial,
    article text
);

insert into articles (article)
values ('article 1'),
       ('article 2'),
       ('article 3'),
       ('article 4'),
       ('article 5');
```

### Query

```sql
select * from articles;

 id |  article  
----+-----------
  1 | article 1
  2 | article 2
  3 | article 3
  4 | article 4
  5 | article 5


select *
from deleted_articles;

 id | article 
----+---------
(0 rows)

-- deleting from one table
-- returning from

with cte_delete_article as (
    delete from articles
        where id = 1
        returning *
)
insert
into deleted_articles
select *
from cte_delete_article;

select *
from deleted_articles;

-- output 

  id |  article  
----+-----------
  1 | article 1
(1 row)
```
