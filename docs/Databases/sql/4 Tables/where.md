---
description: '??'
---

# WHERE Clause

Operators Available

| Operator | Description |
| :--- | :--- |
| = | Equal |
| &gt; | Greater than |
| &lt; | Less than |
| &gt;= | Greater than or equal |
| &lt;= | Less than or equal |
| &lt;&gt; or != | Not equal |
| AND | Logical operator AND |
| OR | Logical operator OR |
| [IN](https://www.postgresqltutorial.com/postgresql-in/) | Return true if a value matches any value in a list |
| [BETWEEN](https://www.postgresqltutorial.com/postgresql-between/) | Return true if a value is between a range of values |
| [LIKE](https://www.postgresqltutorial.com/postgresql-like/) | Return true if a value matches a pattern |
| [IS NULL](https://www.postgresqltutorial.com/postgresql-is-null/) | Return true if a value is NULL |
| NOT | Negate the result of other operators |

### Where 

* Cannot use column alias with where clause

```sql
SELECT select_list
FROM table_name
WHERE condition
ORDER BY sort_expression
```

### Where with OR

```sql
select first_name, last_name, date_of_birth
from actors
where date_of_birth < '1990-01-01' 
   or date_of_birth > '1980-01-01'
LIMIT 10;

 first_name | last_name |    date_of_birth    
------------+-----------+---------------------
 Malin      | Akerman   | 1978-05-12
 Tim        | Allen     | 1953-06-13
 Julie      | Andrews   | 1935-10-01
 Ivana      | Baquero   | 1994-06-11
 Lorraine   | Bracco    | 1954-10-02
 Alice      | Braga     | 1983-04-15
 Marlon     | Brando    | 1924-04-03
 Adrien     | Brody     | 1973-04-14
 Peter      | Carlberg  | 1950-12-08
 Gemma      | Chan      | 1982-11-29

select first_name, last_name, date_of_birth
from actors
where date_of_birth < '1990-01-01'
   or date_of_birth > '1980-01-01'
ORDER BY date_of_birth
LIMIT 10;

-- with order by clause
 first_name | last_name |    date_of_birth    
------------+-----------+---------------------
 Clark      | Gable     | 1901-02-01 00:00:00
 Scatman    | Crothers  | 1910-05-23 00:00:00
 Vivien     | Leigh     | 1913-11-05 00:00:00
 Alec       | Guiness   | 1914-04-02 00:00:00
 Judy       | Garland   | 1922-06-10 00:00:00
 Marlon     | Brando    | 1924-04-03 00:00:00
 Dick       | Van Dyke  | 1925-12-13 00:00:00
 Sihung     | Lung      | 1930-01-01 00:00:00
 Ian        | Holm      | 1931-09-12 00:00:00
 Rebecca    | Pan       | 1931-12-29 00:00:00

```

### Where with AND

```sql
select *
from directors
where date_of_birth > '1970-01-01'
  AND nationality = 'British';
  
 director_id | first_name | last_name | date_of_birth | nationality 
-------------+------------+-----------+---------------+-------------
           4 | Richard    | Ayoade    | 1977-06-12    | British
          18 | Martin     | McDonagh  | 1970-03-26    | British

```

### Where with BETWEEN \(NOT BETWEEN\)

```sql
select *
from directors
where date_of_birth between '1970-01-01' and '1990-01-01';

 director_id | first_name |        last_name         | date_of_birth | nationality 
-------------+------------+--------------------------+---------------+-------------
           2 | Paul       | Anderson                 | 1970-06-26    | American
           4 | Richard    | Ayoade                   | 1977-06-12    | British
          11 | Florian    | Henckel von Donnersmarck | 1973-05-02    | German
          18 | Martin     | McDonagh                 | 1970-03-26    | British

```

### Where with LIKE

```sql
SELECT first_name,
       last_name
FROM actors
WHERE actors.last_name LIKE '%son'
ORDER BY first_name;

 first_name |  last_name  
------------+-------------
 Jack       | Nicholson
 Lina       | Leandersson
 Luke       | Wilson
 Mykelti    | Williamson
 Owen       | Wilson
 Patrick    | Wilson
 Samuel     | Jackson
 Woody      | Harrelson

```

### Where with IN and NOT IN

```sql
select *
from directors
where nationality in ('American', 'Japenese');

 director_id | first_name |        last_name         | date_of_birth | nationality  
-------------+------------+--------------------------+---------------+--------------
           1 | Tomas      | Alfredson                | 1965-04-01    | Swedish
           2 | Paul       | Anderson                 | 1970-06-26    | American
           3 | Wes        | Anderson                 | 1969-05-01    | American
           4 | Richard    | Ayoade                   | 1977-06-12    | British
           5 | Luc        | Besson                   | 1959-03-18    | French
           6 | James      | Cameron                  | 1954-08-16    | American
           7 | Guillermo  | del Toro                 | 1964-10-09    | Mexican
           
select *
from directors
where nationality not in ('American', 'Japenese');

 director_id | first_name |        last_name         | date_of_birth | nationality  
-------------+------------+--------------------------+---------------+--------------
           1 | Tomas      | Alfredson                | 1965-04-01    | Swedish
           4 | Richard    | Ayoade                   | 1977-06-12    | British
           5 | Luc        | Besson                   | 1959-03-18    | French
           7 | Guillermo  | del Toro                 | 1964-10-09    | Mexican
          10 | Kinji      | Fukasaku                 | 1930-07-03    | Japanese
          11 | Florian    | Henckel von Donnersmarck | 1973-05-02    | German
          12 | Terry      | Jones                    | 1942-02-01    | British
```



