# Managing Tables

## Creating new tables with INTO

```sql
SELECT * 
INTO emp3
FROM orders
WHERE employee_id = 3;

SELECT * FROM emp3;

select employee_id, ship_name from emp3 limit 5;

 employee_id |       ship_name        
-------------+------------------------
           3 | Victuailles en stock
           3 | Hanari Carnes
           3 | Wellington Importadora
           3 | Wartian Herkku
           3 | QUICK-Stop
```

## Create table with NOT DATA

Just copying the table structure

```sql
CREATE TABLE emp1 as 
    (SELECT * FROM orders where employee_id = 1) 
    WITH NO DATA;

SELECT * FROM emp1;
```

## Tables are Fraud

* When you UPDATE value in database, it doesnt update the original row
* When you DELETE a row in table, it doesnt show you the new row, but it still remains in db

```sql
drop table table_vacuum;

CREATE TABLE table_vacuum
(
    id integer
);

select pg_total_relation_size('table_vacuum'),
       pg_size_pretty(pg_total_relation_size('table_vacuum'));
-- output : 0 bytes

INSERT INTO table_vacuum
SELECT *
FROM generate_series(1, 400000);

select pg_total_relation_size('table_vacuum'),
       pg_size_pretty(pg_total_relation_size('table_vacuum'));
-- output : 14MB

SELECT *
FROM table_vacuum
limit 5;

update table_vacuum
set id = id + 2;

select pg_total_relation_size('table_vacuum'),
       pg_size_pretty(pg_total_relation_size('table_vacuum'));
-- output : 28MB

SELECT *
FROM table_vacuum
limit 5;

-- look at autovacuum process

    SELECT relname,
           last_vacuum,
           last_autovacuum,
           last_analyze,
           vacuum_count,
           autovacuum_count
    FROM pg_stat_all_tables
        WHERE relname = 'table_vacuum';

VACUUM FULL VERBOSE table_vacuum;

select pg_total_relation_size('table_vacuum'),
       pg_size_pretty(pg_total_relation_size('table_vacuum'));
-- output : 14MB
```

