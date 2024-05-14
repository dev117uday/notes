# Cross & Natural Joins

## Cross Join

```sql
-- every row in the left table will match with every row 
-- in the right table in cross join

select *
from left_product
         cross join right_product;

 product_id | product_name | product_id | product_name 
------------+--------------+------------+--------------
          1 | a            |          1 | a
          1 | a            |          2 | B
          1 | a            |          3 | C
          1 | a            |          4 | d
          1 | a            |          7 | E1
          2 | B            |          1 | a
          2 | B            |          2 | B
          2 | B            |          3 | C
          2 | B            |          4 | d
          2 | B            |          7 | E1
          3 | C            |          1 | a
          3 | C            |          2 | B
          3 | C            |          3 | C
          3 | C            |          4 | d
          3 | C            |          7 | E1
          5 | E            |          1 | a
          5 | E            |          2 | B
          5 | E            |          3 | C
          5 | E            |          4 | d
          5 | E            |          7 | E1


-- short hand notation for cross join
select * from left_product , right_product;
-- same output as above

-- using inner join to preform query like cross join
-- using ON TRUE at the end
select *
from left_product
         inner join right_product on true;
-- same output as above
```

## Natural Join

### Loading sample data

```sql
DROP TABLE IF EXISTS c1;
CREATE TABLE c1
(
    category_id   serial PRIMARY KEY,
    category_name VARCHAR(255) NOT NULL
);

DROP TABLE IF EXISTS p1;
CREATE TABLE p1
(
    product_id   serial PRIMARY KEY,
    product_name VARCHAR(255) NOT NULL,
    category_id  INT          NOT NULL,
    FOREIGN KEY (category_id) REFERENCES c1 (category_id)
);

INSERT INTO c1 (category_name)
VALUES ('Smart Phone'),
       ('Laptop'),
       ('Tablet');

INSERT INTO p1 (product_name, category_id)
VALUES ('iPhone', 1),
       ('Samsung Galaxy', 1),
       ('HP Elite', 2),
       ('Lenovo Think pad', 2),
       ('iPad', 3),
       ('Kindle Fire', 3);

```

### Queries

```sql
SELECT *
FROM p1
         NATURAL JOIN c1;

 category_id | product_id |   product_name   | category_name 
-------------+------------+------------------+---------------
           1 |          1 | iPhone           | Smart Phone
           1 |          2 | Samsung Galaxy   | Smart Phone
           2 |          3 | HP Elite         | Laptop
           2 |          4 | Lenovo Think pad | Laptop
           3 |          5 | iPad             | Tablet
           3 |          6 | Kindle Fire      | Tablet

--same query using inner join
SELECT *
FROM p1
         INNER JOIN c1 USING (category_id);
-- same output as above
```

