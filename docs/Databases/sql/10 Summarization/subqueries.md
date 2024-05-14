# SubQueries

* Allows you to construct  a complex query
* A sub-query is nested inside another query
* can be nested inside `SELECT`, `INSERT`, `UPDATE`, `DELETE`

## SubQueries with SELECT clause

```sql
SELECT movie_name,
       movie_length
FROM movies mv
WHERE movie_length >= (
    SELECT avg(movie_length)
    FROM movies
)

SELECT movie_name,
       movie_length
FROM movies mv
WHERE movie_length >= (
    SELECT avg(movie_length)
    FROM movies
    WHERE movie_lang = 'English'
);

```

## SubQueries With WHERE Clause

```sql

SELECT first_name, last_name, date_of_birth
FROM actors
WHERE date_of_birth > (
    SELECT date_of_birth
    FROM actors
    WHERE first_name = 'Douglas' -- 1922-06-10
);

-- using IN operator
SELECT movie_name,
       movie_length
FROM movies
WHERE movie_id in (
    SELECT movie_id
    FROM movies_revenues
    WHERE revenues_domestic > 200
);

SELECT movie_id, movie_name
FROM movies
WHERE movie_id IN (
    SELECT movie_id
    FROM movies_revenues
    WHERE revenues_domestic > movies_revenues.revenues_international
);

```

## SubQueries with JOINS

```sql
-- with joins
SELECT d.director_id,
       d.first_name || ' ' || d.last_name  as "Director Name",
       SUM(r.revenues_international + r.revenues_domestic) as "total_revenues"
FROM directors d
         INNER JOIN movies mv ON mv.director_id = d.director_id
         INNER JOIN movies_revenues r on r.movie_id = mv.movie_id
WHERE (r.revenues_domestic + r.revenues_international) >
      (
          SELECT avg(revenues_domestic + revenues_international) as "avg_total_revenue"
          FROM movies_revenues
      )
GROUP BY d.director_id
ORDER BY total_revenues;


SELECT d.director_id,
       SUM(COALESCE(r.revenues_domestic, 0) + COALESCE(r.revenues_international, 0)) AS "totaL_reveneues"
FROM directors d
         INNER JOIN movies mv ON mv.director_id = d.director_id
         INNER JOIN movies_revenues r ON r.movie_id = mv.movie_id
WHERE COALESCE(r.revenues_domestic, 0) + COALESCE(r.revenues_international, 0) >
      (
          SELECT AVG(COALESCE(r.revenues_domestic, 0) + COALESCE(r.revenues_international, 0)) as "avg_total_reveneues"
          FROM movies_revenues r
                   INNER JOIN movies mv ON mv.movie_id = r.movie_id
          WHERE mv.movie_lang = 'English'
      )
GROUP BY d.director_id
ORDER BY 2 DESC, 1 ASC;
```

## SubQueries with Alias

```sql
-- as alias
SELECT *
FROM (
         SELECT *
         FROM movies
     ) t1;

-- query without FROM
SELECT (
           SELECT avg(revenues_domestic) as "Average Revenue"
           FROM movies_revenues
       ),
       (
           SELECT min(revenues_domestic) as "MIN Revenue"
           FROM movies_revenues
       ),
       (
           SELECT max(revenues_domestic) as "MAX Revenue"
           FROM movies_revenues
       )
```

