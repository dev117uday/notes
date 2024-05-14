---
description: one day, you will need them
---

# Internal Functions

**Order of execution of SQL statements**

1. FROM
2. WHERE
3. SELECT
4. ORDER BY

## Concatenation Operator

```sql
select concat(first_name,last_name) as full_name 
    from directors limit 10;
    
   full_name    
----------------
 TomasAlfredson
 PaulAnderson
 WesAnderson

select concat_ws(' ',first_name,last_name) as full_name 
    from directors limit 3;

    full_name    
-----------------
 Tomas Alfredson
 Paul Anderson
 Wes Anderson
```

* if you can have a **null** **value** in column, always use `concat_ws` because it will place nothing in that and and also not place the spacer like **|** or a space

## Type Conversion

| Type of Conversion | Notes                                                                 |
| ------------------ | --------------------------------------------------------------------- |
| Implicit           | data conversion is done AUTOMATICALLY                                 |
| Explicit           | data conversion is done via 'conversion functions' eg. `CAST` or `::` |

```sql
SELECT * FROM movies;

-- exact datatype match : no conversion
SELECT * FROM movies WHERE movie_id = 1;

-- Implicit conversion : conversion
SELECT * FROM movies WHERE movie_id = '1';

-- Explicit conversion : conversion
SELECT * FROM movies WHERE movie_id = integer '1';

-- Output of all queries above
 movie_id |     movie_name     | movie_length | movie_lang | release_date | age_certificate | director_id 
----------+--------------------+--------------+------------+--------------+-----------------+-------------
        1 | A Clockwork Orange |          112 | English    | 1972-02-02   | 18              |          13
```

## Casting

```sql
-- CAST function
-- Syntax : CAST ( expression as target_data_type );

SELECT CAST ( '10' AS INTEGER );

 int4 
------
   10

SELECT 
    CAST ('2020-02-02' AS DATE), 
    CAST('01-FEB-2001' AS DATE);
    
    date    |    date    
------------+------------
 2020-02-02 | 2001-02-01

SELECT 
    CAST ( 'true' AS BOOLEAN ), 
    CAST ( '1' AS BOOLEAN ), 
    CAST ( '0' AS BOOLEAN );

 bool | bool | bool 
------+------+------
 t    | t    | f

SELECT CAST ( '14.87789' AS DOUBLE PRECISION );

  float8  
----------
 14.87789

SELECT '2020-02-02'::DATE , '01-FEB-2001'::DATE;

    date    |    date    
------------+------------
 2020-02-02 | 2001-02-01

SELECT '2020-02-02 10:20:10.23'::TIMESTAMP;

       timestamp        
------------------------
 2020-02-02 10:20:10.23

SELECT '2020-02-02 10:20:10.23 +05:30'::TIMESTAMPTZ;

        timestamptz        
---------------------------
 2020-02-02 04:50:10.23+00

SELECT 
    '10 minute'::interval, 
    '10 hour'::interval, 
    '10 day'::interval, 
    '10 week'::interval, 
    '10 month'::interval;

 interval | interval | interval | interval | interval 
----------+----------+----------+----------+----------
 00:10:00 | 10:00:00 | 10 days  | 70 days  | 10 mons

SELECT 
    20! AS "result 1" , 
    CAST( 20 AS bigint ) ! AS "result 2";

      result 1       |      result 2       
---------------------+---------------------
 2432902008176640000 | 2432902008176640000

SELECT 
    ROUND(10,4) AS "result 1", 
    ROUND ( CAST (10 AS NUMERIC) ) AS "result 2", 
    ROUND ( CAST (10 AS NUMERIC) , 4 ) AS "result 3";

 result 1 | result 2 | result 3 
----------+----------+----------
  10.0000 |       10 |  10.0000

SELECT 
    SUBSTR('12345',2) AS "RESULT 1", 
    SUBSTR( CAST('12345' AS TEXT) ,2) AS "RESULT 2";

 RESULT 1 | RESULT 2 
----------+----------
 2345     | 2345
```

```sql
CREATE TABLE ratings (
    rating_id SERIAL PRIMARY KEY,
    rating VARCHAR(2) NOT NULL
);

INSERT INTO ratings ( rating ) 
VALUES ('A'), ('B'), ('C'), ('D'), (1), (2), (3), (4);

SELECT 
    rating_id,
    CASE 
        WHEN rating~E'^\\d+$' THEN
            CAST ( rating as INTEGER )
        ELSE
            0
        END AS rating
FROM
    ratings;
    
 rating_id | rating 
-----------+--------
         1 |      0
         2 |      0
         3 |      0
         4 |      0
         5 |      1
         6 |      2
         7 |      3
         8 |      4
```

## Formatting Functions

[https://www.postgresql.org/docs/12/functions-formatting.html](https://www.postgresql.org/docs/12/functions-formatting.html)

### to\_char()

Refer to the documentation

- https://www.postgresqltutorial.com/postgresql-to_char/

```sql
SELECT TO_CHAR (
    100870,
    '9,999999'
);    

  to_char  
-----------
    100870

SELECT 
    release_date, 
    TO_CHAR(release_date,'DD-MM-YYYY'),
    TO_CHAR(release_date,'Dy, MM, YYYY') 
FROM 
    movies LIMIT 3;
    
 release_date |  to_char   |    to_char    
--------------+------------+---------------
 1972-02-02   | 02-02-1972 | Wed, 02, 1972
 1979-08-15   | 15-08-1979 | Wed, 08, 1979
 2001-01-04   | 04-01-2001 | Thu, 01, 2001

SELECT 
    TO_CHAR ( 
        TIMESTAMP '2020-01-01 13:32:30', 
        'HH24:MI:SS' 
    );
    
 to_char  
----------
 13:32:30
```

### to\_number()

- https://www.postgresqltutorial.com/postgresql-to_number/

```sql
SELECT TO_NUMBER(
    '1420.89', '9999.'
);

 to_number 
-----------
      1420

SELECT TO_NUMBER(
    '10,625.78-', '99G999D99S'
);

 to_number 
-----------
 -10625.78

SELECT TO_NUMBER(
    '$1,625.78+', '99G999D99S'
);

 to_number 
-----------
   1625.78

SELECT to_number(
    '$1,420.65' , 'L9G999D99'
);

 to_number 
-----------
   1420.65

SELECT to_number(
    '21,420.65' , '99G999D99'
);

 to_number 
-----------
  21420.65
```

### to\_date()

- https://www.postgresqltutorial.com/postgresql-to_date/

```sql
SELECT TO_DATE( '2020/10/22' , 'YYYY/MM/DD' );

  to_date   
------------
 2020-10-22

SELECT to_date( '022199' , 'MMDDYY' );

  to_date   
------------
 1999-02-21

SELECT to_date( 'March 07, 2019' , 'Month DD, YYYY' );

    to_date   
------------
 2019-03-07
```

### to\_timestamp()

- https://www.postgresqltutorial.com/postgresql-to_timestamp/

```sql
SELECT TO_TIMESTAMP(
    '2017-03-31 9:30:20',
    'YYYY-MM-DD HH:MI:SS'
);

      to_timestamp      
------------------------
 2017-03-31 09:30:20+00

SELECT
    TO_TIMESTAMP('2017     Aug','YYYY MON');
    
      to_timestamp      
------------------------
 2017-08-01 00:00:00+00
```

## String Functions

* `Upper(string)`&#x20;
* `Lower(string)`
* `INITCAP(string)`
* `REVERSE(string)`
* `LPAD(string)`
* `RPAD(string)`
* `LENGTH(string)`
* `CHAR_LENGTH(string) : Same as Length`
* `POSITION( string in string )`
* `STRPOS ( , < substring > )`
* `SUBSTRING (string , length)`
* `REPLACE (string, from_string, to_string)`

```sql
SELECT INITCAP(first_name) as FirstName,
       INITCAP(last_name)  as LastName
FROM directors
LIMIT 3;

 firstname | lastname
-----------+-----------
 Tomas     | Alfredson
 Paul      | Anderson
 Wes       | Anderson

SELECT LEFT('Uday', 3), RIGHT('Uday', 3);

 left | right
------+-------
 Uda  | day

SELECT LEFT('Uday', -3), RIGHT('Uday', -3);

 left | right
------+-------
 U    | y

SELECT REVERSE('UDAY YADAV');

  reverse
------------
 VADAY YADU

SELECT SPLIT_PART('1,2,3,4', ',', 1), 
       SPLIT_PART('1|2|3|4', '|', 2);

 split_part | split_part
------------+------------
 1          | 2

SELECT TRIM(LEADING FROM '  Amazing PostgreSQL'),
       TRIM(TRAILING FROM 'Amazing PostgreSQL  '),
       TRIM('  Amazing PostgreSQL  ');

       ltrim        |       rtrim        |       btrim
--------------------+--------------------+--------------------
 Amazing PostgreSQL | Amazing PostgreSQL | Amazing PostgreSQL


SELECT TRIM(LEADING '0' FROM CAST(0001245 AS TEXT));

 ltrim
-------
 1245


SELECT LTRIM('yummy', 'y'),
       RTRIM('yummy', 'y'),
       BTRIM('yummy', 'y');

 ltrim | rtrim | btrim
-------+-------+-------
 ummy  | yumm  | umm


SELECT upper('uday yadav'),  initcap('uday yadav');

   upper    |  initcap
------------+------------
 UDAY YADAV | Uday Yadav

SELECT INITCAP(first_name) as FirstName,
       INITCAP(last_name)  as LastName
FROM directors LIMIT 3;

 firstname | lastname
-----------+-----------
 Tomas     | Alfredson
 Paul      | Anderson
 Wes       | Anderson

SELECT LEFT('Uday', 3), RIGHT('Uday', 3);
 left | right
------+-------
 Uda  | day

SELECT LEFT('Uday', -3), RIGHT('Uday', -3);
 left | right
------+-------
 U    | y


SELECT LPAD('Database', 15, '*'),
       RPAD('Database', 15, '*');

      lpad       |      rpad
-----------------+-----------------
 *******Database | Database*******

SELECT LENGTH('Uday Yadav');

 length
--------
     10

SELECT LENGTH(CAST(10013 AS TEXT));
 length
--------
      5


SELECT char_length(''),
       char_length('  '),
       char_length(NULL);

 char_length | char_length | char_length
-------------+-------------+-------------
           0 |           2 |

SELECT first_name || ' ' || last_name as FullName,
       LENGTH(first_name || ' ' || last_name)
                                      as FullNameLength
FROM Directors
ORDER BY 2 DESC LIMIT 2;

             fullname              | fullnamelength
-----------------------------------+----------------
 Florian  Henckel von Donnersmarck |             33
 Francis Ford Coppola              |             20

SELECT POSITION('Amazing' IN 'Amazing PostgreSQL'),
       POSITION('is' IN 'This is a computer');
 position | position
----------+----------
        1 |        3

SELECT STRPOS('World Bank', 'Bank');
 strpos
--------
      7

SELECT first_name,
       last_name
FROM directors
WHERE strpos(last_name, 'on') > 0 LIMIT 3;

 first_name | last_name
------------+-----------
 Tomas      | Alfredson
 Paul       | Anderson
 Wes        | Anderson

SELECT substring('What a wonderful world' from 1 for 10);

 substring
------------
 What a won

SELECT repeat('A', 4), repeat(' ', 9), repeat('.', 8);
 repeat |  repeat   |  repeat
--------+-----------+----------
 AAAA   |           | ........

SELECT REPLACE('ABC XYZ', 'XY', 'Z');
 replace 
---------
 ABC ZZ
```
