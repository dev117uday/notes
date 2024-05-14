# Views

## Plain View

A **view** is a database object that is a stored query. A view is a virtual table you can create dynamically using a saved query acting as a `virtual table.`

* You can join a view to another table or view
* You can query a view
* Regular views don't store any data, but materialised view does

### Syntax

```sql
CREATE OR REPLACE VIEW view_name AS query
```

### Example 1

```sql
CREATE OR REPLACE VIEW v_movie_quick AS
SELECT movie_name,
       movie_length,
       release_date
from movies mv;

-- use just like a normal table
select * from v_movie_quick limit 5;

     movie_name     | movie_length | release_date 
--------------------+--------------+--------------
 A Clockwork Orange |          112 | 1972-02-02
 Apocalypse Now     |          168 | 1979-08-15
 Battle Royale      |          111 | 2001-01-04
 Blade Runner       |          121 | 1982-06-25
 Chungking Express  |          113 | 1996-08-03

```

### Example with JOIN

```sql
CREATE OR REPLACE VIEW v_movie_d_name as
SELECT movie_name,
       movie_length,
       release_date,
       d.first_name || ' ' || d.last_name as "full name"
from movies mv
         inner join directors d 
          on mv.director_id = d.director_id;

select * from v_movie_d_name limit 5;

       movie_name       | movie_length | release_date |    full name    
------------------------+--------------+--------------+-----------------
 Let the Right One In   |          128 | 2008-10-24   | Tomas Alfredson
 There Will Be Blood    |          168 | 2007-12-26   | Paul Anderson
 The Darjeeling Limited |          119 | 2007-09-29   | Wes Anderson
 Rushmore               |          104 | 1998-11-12   | Wes Anderson
 Grand Budapest Hotel   |          117 | 2014-07-03   | Wes Anderson
```

### Managing VIEW

```sql
ALTER VIEW v_movie_d_name RENAME TO v_movie_with_names;
-- ALTER VIEW

select * from v_movie_with_names limit 5;
-- SAME OUTPUT AS ABOVE

-- dropping view
DROP VIEW if exists v_movie_quick;
```

### View containing condition

```sql
CREATE OR REPLACE VIEW v_movie_after_1997 as
select *
from movies
where release_date >= '1997-12-31'
  and movie_lang = 'English'
order by release_date desc limit 5;

 movie_id |                 movie_name                 | movie_length | movie_lang | release_date | age_certificate | director_id 
----------+--------------------------------------------+--------------+------------+--------------+-----------------+-------------
       47 | Three Billboards Outside Ebbing, Missouri  |          134 | English    | 2017-11-10   | 15              |          18
       15 | Grand Budapest Hotel                       |          117 | English    | 2014-07-03   | PG              |           3
       22 | Life of Pi                                 |          129 | English    | 2012-11-21   | PG              |          15
       38 | Submarine                                  |          115 | English    | 2011-06-03   | 15              |           4
       24 | Never Let Me Go                            |          117 | English    | 2010-09-15   | 15              |          25
```

**You cannot add, update, delete columns from a view once created for that create a new view, drop the old view and rename the new view back to original**

#### **Deleting from view also deletes from the main table**

```sql
CREATE OR REPLACE VIEW v_movie as
select *
from movies;

select *
from v_movie limit 5;

select *
from movies limit 5;

-- deleting from v_movie also deletes from main table
delete
from v_movie
where movie_id = 4;
```

## Materialized View

### Allows you to

* store result of a query
* update data periodically :: manual
* used to cache result of heavy data

  syntax

```sql
CREATE MATERIALIZED VIEW IF NOT EXISTS view_name 
AS query WITH [ NO ] DATA;
```

**If you want to load data into the materialised view at the creation time, you will use `WITH DATA`**

```sql
create materialized view if not exists mv_dir as
select first_name, last_name
from directors
with no data;

-- this will give error becuz no data
-- is loaded at time of creation
select * from mv_dir limit 5;

-- refreshing view to load data in view
refresh materialized view  mv_dir;

select * from mv_dir limit 5;

-- dropping materialized view
drop materialized view mv_dir;
```

* **cannot change data** in `materialised view` : _insert, update and delete_ 
* **advantage** of using`materialised view` : access and update `materialised view` without locking everyone else out 
* **disadvantage** of using `materialised view` if you alter the base table , 

  `materialised view` must also me alter : delete the old `materialised view` and create a new one

