# Usefull Functions

## Case

```sql
SELECT movie_id,
       movie_name,
       CASE
           WHEN movies.movie_length < 100
               AND movies.movie_length <= 50 THEN 'Short'
           WHEN movies.movie_length > 100
               AND movies.movie_length <= 130 THEN 'Medium'
           WHEN movies.movie_length > 130 THEN 'Long'
           END duration
FROM movies
ORDER BY movie_name;
LIMIT 10;

 movie_id |          movie_name           | duration 
----------+-------------------------------+----------
        1 | A Clockwork Orange            | Medium
        2 | Apocalypse Now                | Long
        3 | Battle Royale                 | Medium
        4 | Blade Runner                  | Medium
        5 | Chungking Express             | Medium
        6 | City of God                   | Long
        7 | City of Men                   | Long
        8 | Cold Fish                     | Medium
        9 | Crouching Tiger Hidden Dragon | Long
       10 | Eyes Wide Shut                | Medium
(10 rows)


SELECT
       SUM(CASE age_certificate
             WHEN '12' THEN 1 
		     ELSE 0 
		   END) "Kids",
       SUM(CASE age_certificate
             WHEN '15' THEN 1 
		     ELSE 0 
		   END) "School",
       SUM(CASE age_certificate
             WHEN '18' THEN 1 
		     ELSE 0 
		   END) "Teens",
       SUM(CASE age_certificate
             WHEN 'PG' THEN 1 
		     ELSE 0 
		   END) "Restricted",
       SUM(CASE age_certificate
             WHEN 'U' THEN 1 
		     ELSE 0 
		   END) "Universal"
FROM movies;

  Kids | School | Teens | Restricted | Universal 
------+--------+-------+------------+-----------
   11 |     16 |     8 |         12 |         6
```

## Coalesce

`COALESCE` function that returns the first non-null argument.

```sql
SELECT
	COALESCE (NULL, 2 , 1);
```

## NULLIF

The `NULLIF` function returns a null value if `argument_1` equals to `argument_2`, otherwise it returns `argument_1`

```sql
SELECT
	NULLIF (1, 1); -- return NULL

SELECT
	NULLIF (1, 0); -- return 1

SELECT
	NULLIF ('A', 'B'); -- return A
```

## Cube

* First, specify the `CUBE` subclause in the the [`GROUP BY`](https://www.postgresqltutorial.com/postgresql-group-by/) clause of the [`SELECT`](https://www.postgresqltutorial.com/postgresql-select/) statement.
* Second, in the select list, specify the columns \(dimensions or dimension columns\) which you want to analyze and [aggregation function](https://www.postgresqltutorial.com/postgresql-aggregate-functions/) expressions.
* Third, in the `GROUP BY` clause, specify the dimension columns within the parentheses of the `CUBE` subclause.

```sql
select age_certificate, movie_length
from movies
group by age_certificate, cube (age_certificate, movie_length )
order by age_certificate;

```

