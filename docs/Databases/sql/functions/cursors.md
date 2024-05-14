# Cursors

* Rows returned by the SQL query are those which match the condition. It can either be zero or more at a time.
* Sometimes you need to traverse through the rows one by one, forward or backwards
* Life Cycle of Cursor
  * DECLARE 
  * OPEN
  * FETCH
  * CLOSE
* Cursor enable SQL to retrieve ( or update, or delete ) a single row at a time.
* Cursor needs to be created in 

```sql
DECLARE cur_al_movie refcursor;

-- or

cursor-name [cursor-scrollability] cursor [(name datatype ...)]
FOR
    query-expression
```    

- `cursor-scrollability` :  `SCROLL` OR `NO SCROLL`, `NO SCROLL` mean the cursor cannot scrol backward.
- `query-expression` : You can use any legal SELECT statement as a query expression. The result set rows are considered as scope of the cursor.

## Example

```sql
DECLARE cur_all_movies CURSOR
    FOR
        SELECT movie_name, movie_length FROM movies;
```

## Cursor with Parameters

```sql
DECLARE cur_all_movies_by_year CURSOR ( custom_year integer )
FOR
    SELECT  
        *
    FROM movies
    WHERE EXTRACT ('YEAR' FROM release_date ) = custom_year
```

## Opening a cursor

- Opening an unbound cursor

```sql
OPEN unbound_cursor_variable [[NO] SCROLL] FOR query;
```

## Opening un bound cursor

```sql
OPEN cur_directors_us
FOR
    SELECT
        first_name,
        last_name,
        date_of_birth
    FROM
        directors
    WHERE
        nationality = 'American'
```

## Opening an un bound cursor with dynamic query

```sql
OPEN unbound_cursor_variable [[NO] SCROLL]
FOR EXECUTE
    query-expression [using expression [,...]];
```

```sql
select * from movies order by movie_name;

DO
$$

    DECLARE
        output_text text default '';
        rec_movie record;

        cur_all_movies CURSOR
        FOR
            SELECT * FROM movies;

    BEGIN

        OPEN cur_all_movies;

        LOOP

            FETCH cur_all_movies into rec_movie;
                EXIT WHEN NOT FOUND;

            output_text := output_text || ',' || rec_movie.movie_name;

        END LOOP;

        RAISE NOTICE 'ALL MOVIES NAME %' , output_text;

    END;

$$


    
```