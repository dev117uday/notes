---
description: the usual
---

# JSON

## JSON

### JSON vs JSONB

| JSON | JSONB |
| :--- | :--- |
| stores data in text format | stores data in binary format |
| stores data AS-is | trims of white spaces |
| slower in operations | fASter in operations |
| doesn't support full text indexing | supports full text indexing |

```sql
SELECT '{
    "title":"book 1"}
'::json;

         json          
-----------------------
 {                    +
     "title":"book 1"}+

(1 row)


SELECT '
  {"title":"book 1"}
  '::jsonb


        jsonb        
---------------------
 {"title": "book 1"}
(1 row)
```

### Operations

```sql
CREATE TABLE books_jsonb
(
    id        serial primary key,
    book_info JSONB
);

INSERT INTO books_jsonb (book_info)
VALUES ('{
  "title": "Book 1"
}'),
('{
  "title": "Book 2"
}'),
('{
  "title": "Book 3"
}');

 id | title  
----+--------
  1 | Book 1
  2 | Book 2
  3 | Book 3


SELECT id, book_info ->> 'title' AS "title"
FROM books_jsonb
WHERE book_info ->> 'title' = 'Book 1';

 id | title  
----+--------
  1 | Book 1


INSERT INTO books_jsonb (book_info)
VALUES ('{ "title": "Book 10" }');


 id |      book_info       
----+----------------------
  1 | {"title": "Book 1"}
  2 | {"title": "Book 2"}
  3 | {"title": "Book 3"}
  4 | {"title": "Book 10"}


UPDATE books_jsonb
SET book_info = book_info || '{"title": "Book 4" }'
WHERE book_info ->> 'title' = 'Book 10';

 id |      book_info      
----+---------------------
  1 | {"title": "Book 1"}
  2 | {"title": "Book 2"}
  3 | {"title": "Book 3"}
  4 | {"title": "Book 4"}


UPDATE books_jsonb
SET book_info = book_info || '{"author": "author 1" }'
WHERE book_info ->> 'title' = 'Book 1';

 id |                 book_info                 
----+-------------------------------------------
  2 | {"title": "Book 2"}
  3 | {"title": "Book 3"}
  4 | {"title": "Book 4"}
  1 | {"title": "Book 1", "author": "author 1"}


UPDATE books_jsonb
SET book_info = book_info - 'author'
WHERE book_info ->> 'title' = 'Book 1';

 id |      book_info      
----+---------------------
  1 | {"title": "Book 1"}
  2 | {"title": "Book 2"}
  3 | {"title": "Book 3"}
  4 | {"title": "Book 4"}


UPDATE books_jsonb
SET book_info = book_info || '{"available":["new delhi","Tokyo","sydney"]}'
WHERE book_info ->> 'title' = 'Book 1';

 id |  book_info                                         

  2 | {"title": "Book 2"}
  3 | {"title": "Book 3"}
  4 | {"title": "Book 4"}
  1 | {"title": "Book 1", "author": "author 1", "available": ["new delhi", "Tokyo", "sydney"]}


UPDATE books_jsonb
SET book_info = book_info #- '{available,1}'
WHERE book_info ->> 'title' = 'Book 1';

 id |   Book_info                                    

  2 | {"title": "Book 2"}
  3 | {"title": "Book 3"}
  4 | {"title": "Book 4"}
  1 | {"title": "Book 1", "author": "author 1", "available": ["new delhi", "sydney"]}
```

### ROW\_TO\_JSON\(\)

```sql
SELECT row_to_json(orders)
FROM orders;

 {"order_id":10248,"customer_id":"VINET","employee_id":5,"order_date":"1996-07-04","required_date":"1996-08-01","shipped_date":"1996-07-16","ship_via":3,"freight":32.38,"ship_name":"Vins et alcools Chevalier","ship_address":"59 rue de l'Abbaye","ship_city":"Reims","ship_region":null,"ship_postal_code":"51100","ship_country":"France"}

SELECT row_to_json(t)
FROM 
(
  SELECT *
  FROM orders
) AS t;

 {"order_id":10248,"customer_id":"VINET","employee_id":5,"order_date":"1996-07-04","required_date":"1996-08-01","shipped_date":"1996-07-16","ship_via":3,"freight":32.38,"ship_name":"Vins et alcools Chevalier","ship_address":"59 rue de l'Abbaye","ship_city":"Reims","ship_region":null,"ship_postal_code":"51100","ship_country":"France"}
```

### JSON\_AGG\(\)

```sql
SELECT *
FROM orders;

SELECT director_id, first_name, lASt_name, 
(
  SELECT json_agg(x)
  FROM 
    (
        SELECT movie_name
        FROM movies mv
        WHERE mv.director_id = directors.director_id
    ) AS x
) :: jsonb
FROM directors;
```

### JSON\_BUILD

```sql
SELECT json_build_array(1, 2, 3, 4, 5, 6);

  json_build_array  
--------------------
 [1, 2, 3, 4, 5, 6]


SELECT json_build_array(1, 2, 3, 4, 5, 6, 'Hi');

     json_build_array     
--------------------------
 [1, 2, 3, 4, 5, 6, "Hi"]


-- error : argument list must have even number of elements
SELECT json_build_object(1, 2, 3, 4, 5);

SELECT json_build_object(1, 2, 3, 4, 5, 6, 7, 'Hi');

            json_build_object            
-----------------------------------------
 {"1" : 2, "3" : 4, "5" : 6, "7" : "Hi"}


SELECT json_object('{name,email}', '{"adnan","a@b.com"}');

               json_object               
-----------------------------------------
 {"name" : "adnan", "email" : "a@b.com"}
```

### Json Functions

```sql
CREATE TABLE directors_docs
(
    id   serial primary key,
    body jsonb
);


SELECT director_id,
       first_name,
       last_name,
       (
           SELECT json_agg(x) AS all_movies
           FROM (
                    SELECT movie_name
                    FROM movies mv
                    WHERE mv.director_id = directors.director_id
                ) x
       ) :: jsonb
FROM directors;


INSERT INTO directors_docs (body)
SELECT row_to_json(a)
FROM (
         SELECT director_id,
                first_name,
                last_name,
                (
                    SELECT json_agg(x) AS all_movies
                    FROM (
                             SELECT movie_name
                             FROM movies mv
                             WHERE mv.director_id = directors.director_id
                         ) x
                ) :: jsonb
         FROM directors
) AS a;

SELECT *
FROM directors_docs LIMIT 3;

  1 | {"last_name": "Alfredson", "all_movies": [{"movie_name": "Let the Right One In"}], "first_name": "Tomas", "director_id": 1}
  2 | {"last_name": "Anderson", "all_movies": [{"movie_name": "There Will Be Blood"}], "first_name": "Paul", "director_id": 2}
  3 | {"last_name": "Anderson", "all_movies": [{"movie_name": "Grand Budapest Hotel"}, {"movie_name": "Rushmore"}, {"movie_name": "The Darjeeling Limited"}], "first_name": "Wes", "director_id": 3}


SELECT *, jsonb_array_length(body -> 'all_movies') AS total_movies
FROM directors_docs
order by jsonb_array_length(body->'all_movies') DESC;

 13 | {"last_name": "Kubrick", "all_movies": [{"movie_name": "A Clockwork Orange"}, {"movie_name": "Eyes Wide Shut"}, {"movie_name": "The Shining"}], "first_name": "Stanley", "director_id": 13}                                   |            3
  3 | {"last_name": "Anderson", "all_movies": [{"movie_name": "Grand Budapest Hotel"}, {"movie_name": "Rushmore"}, {"movie_name": "The Darjeeling Limited"}], "first_name": "Wes", "director_id": 3}                                |            3
 17 | {"last_name": "Lucas", "all_movies": [{"movie_name": "Star Wars: A New Hope"}, {"movie_name": "Star Wars: Empire Strikes Back"}, {"movie_name": "Star Wars: Return of the Jedi"}], "first_name": "George", "director_id": 17} |            3


SELECT *,jsonb_object_keys(body) FROM directors_docs;

  1 | {"last_name": "Alfredson", "all_movies": [{"movie_name": "Let the Right One In"}], "first_name": "Tomas", "director_id": 1} | last_name
  1 | {"last_name": "Alfredson", "all_movies": [{"movie_name": "Let the Right One In"}], "first_name": "Tomas", "director_id": 1} | all_movies
  1 | {"last_name": "Alfredson", "all_movies": [{"movie_name": "Let the Right One In"}], "first_name": "Tomas", "director_id": 1} | first_name


SELECT j.key, j.value
FROM directors_docs,
     jsonb_each(body) j;

    key     |                  value                   
------------+------------------------------------------
 last_name  | "Alfredson"
 all_movies | [{"movie_name": "Let the Right One In"}]
 first_name | "Tomas"
```

### Existence Operators

```sql
SELECT *
FROM directors_docs
WHERE body -> 'first_name' ? 'John';

 14 | {"last_name": "Lasseter", "all_movies": [{"movie_name": "Toy Story"}], "first_name": "John", "director_id": 14}
```

### Searching JSON

```sql
SELECT *
FROM directors_docs
WHERE body @> '{"first_name":"John"}';


SELECT *
FROM directors_docs
WHERE body @> '{"director_id":1}';

-- error : No operator matches the given name and argument types. You might need to add explicit type casts.
SELECT *
FROM directors_docs
WHERE body -> 'first_name' LIKE 'J%';


SELECT *
FROM directors_docs
WHERE body ->> 'first_name' LIKE 'J%';

SELECT *
FROM directors_docs
WHERE (body ->> 'director_id')::integer in (1,2,3,4,5,10);
```

