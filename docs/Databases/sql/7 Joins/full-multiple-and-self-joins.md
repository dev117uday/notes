# Full, Multiple & Self Joins

## Full Join

![image](./assets/full-join.png)

```sql
select *
from right_product
  full join 
    left_product
  on 
    right_product.product_id = left_poduct.product_id;
    
-- gets result from tables o both side
-- those entries that match the each other are in one row
-- else they are present in seperate row in table
                   
 product_id | product_name | product_id | product_name 
------------+--------------+------------+--------------
          1 | a            |          1 | a
          2 | B            |          2 | B
          3 | C            |          3 | C
          4 | d            |            | 
          7 | E1           |            | 
            |              |          5 | E


select dir.first_name || ' ' || dir.last_name
           as "name",
       mv.movie_name
from directors dir
         full join movies mv 
         on mv.director_id = dir.director_id
         
      name       |       movie_name       
-----------------+------------------------
 Tomas Alfredson | Let the Right One In
 Paul Anderson   | There Will Be Blood
 Wes Anderson    | The Darjeeling Limited
 Wes Anderson    | Rushmore
 Wes Anderson    | Grand Budapest Hotel
```

## Multiple Join

```sql
select r.movie_id, movie_name,
       dir.first_name || ' ' || dir.last_name 
         as "dir name"
from movies mv
         JOIN movies_revenues r
             on r.movie_id = mv.movie_id
         JOIN directors dir
             on dir.director_id = mv.director_id
limit 5;

 movie_id |     movie_name     |     dir name     
----------+--------------------+------------------
       45 | The Wizard of Oz   | Victor Fleming
       13 | Gone with the Wind | Victor Fleming
       23 | Mary Poppins       | Robert Stevenson
       44 | The Sound of Music | Robert Wise

-- same result even after re-arranging
select r.movie_id,
       movie_name,
       dir.first_name || ' ' || dir.last_name 
         as "dir name"
from movies mv
         JOIN directors dir
              on dir.director_id = mv.director_id
         JOIN movies_revenues r
              on r.movie_id = mv.movie_id
limit 5;
```

## Self Join

```sql
select * 
from left_product t1 
INNER JOIN left_product t2 
ON t1.product_id = t2.product_id;

 product_id | product_name | product_id | product_name 
------------+--------------+------------+--------------
          1 | a            |          1 | a
          2 | B            |          2 | B
          3 | C            |          3 | C
          5 | E            |          5 | E

```



