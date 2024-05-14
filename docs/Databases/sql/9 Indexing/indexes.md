# Indexes

## Data structure

* Data structure used : B-Tree
* Self Balancing Index
* SELECT, INSERT, DELETE and sequential access  in logarithmic time
* Can be used for most of operations and column type
* supports unique condition
* Used in Primary Key
* Used with operators 
* Used when pattern matching

## Hash Index

[https://codingsight.com/hash-index-understanding-hash-indexes/](https://codingsight.com/hash-index-understanding-hash-indexes/)

* for equality operators
* not for range 
* Larger than btree in size

## Brin Index

* block range index
* block data -&gt; min to max value
* smaller index
* less costly to maintain than btree index
* Can be used on very large table

```sql
create table t_big
(
    id   serial,
    name text
);

drop table t_big;

insert into t_big (name)
select 'adam'
from generate_series(1, 2000000);


CREATE INDEX CONCURRENTLY brin_index
    ON public.t_big USING brin
    (id);

create index btree_index on t_big(id);

select pg_size_pretty(pg_total_relation_size('t_big'));

select pg_size_pretty(pg_indexes_size('t_big'));

drop index brin_index;

drop index btree_index;

explain analyse
select *
from t_big
where id = 9999;

explain analyse
select id
from t_big
order by id desc limit 100;
```

## Partial Index

* To performance of the query while reducing the index size.

```sql
create index if not exists partial_inx on t_big(id) where id > 1000000;


explain analyse
select *
from t_big
where id = 10000033;


explain analyse
select *
from t_big
where id = 99999;
```

## Expression Index

* PostgreSQL will use this index when WHERE clause or ORDER BY clause in statement
* Very Expensive to use

```sql
CREATE TABLE IF NOT EXISTS t_dates AS
SELECT d, repeat(md5(d::text),10) as padding
    FROM generate_series
        (timestamp '1800-01-01', timestamp '2100-01-01', interval '1 day') s(d);

select * from t_dates limit 10;

vacuum analyse t_dates;

EXPLAIN ANALYSE
SELECT *
FROM t_dates
WHERE d BETWEEN '2001-01-01' AND '2001-01-31';


CREATE INDEX IF NOT EXISTS idx_t_dates_d on t_dates (d);

ANALYSE t_dates;

EXPLAIN ANALYSE
SELECT *
FROM t_dates
WHERE d BETWEEN '2001-01-01' AND '2001-01-31';

EXPLAIN ANALYSE
SELECT *
FROM t_dates
WHERE EXTRACT(DAY FROM d) = 1;

CREATE INDEX idx_expr_t_dates on t_dates (extract(day from d));

ANALYSE t_dates;

EXPLAIN ANALYSE
SELECT *
FROM t_dates
WHERE EXTRACT(DAY FROM d) = 1;
```

## Heap Index

