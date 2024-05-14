# Partitioning Tables

**It is splitting table into**

* logical division
* multiple smaller pieces
* more manageable pieces table

Partition leads to a huge performance boost.

**Types of ranges**

* `Range` : The table is partitioned into "range" defined by a key column or set of columns, with no overlap between the ranges of values assigned to different partitions
* `List` : According to a key in table, ex : country, sales
* `Hash` : The partition specifying a modulus and a reminder for each partition. 

## Table Inheritance

```sql
create table master (
    pk INTEGER primary key ,
    tag text,
    parent integer
);

create table master_child() inherits (master);

ALTER TABLE public.master_child
    ADD PRIMARY KEY (pk);


select * from master;
select * from master_child;

insert into master (pk, tag, parent) values (1,'pencil',0);
insert into master_child (pk, tag, parent) values (2,'pen',0);

update master set tag = 'monitor' where pk = 2;

select * from only master;
select * from only master_child;

-- error
drop table master;
drop table master cascade ;
```

## Range Partitioning

```sql
create table employees_range (
    id bigserial,
    birth_date DATE NOT NULL ,
    country_code VARCHAR(2) NOT NULL
) PARTITION BY RANGE (birth_date);

CREATE TABLE employee_range_y2000 PARTITION of
    employees_range for values from ('2000-01-01') to ('2001-01-01');

CREATE TABLE employee_range_y2001 PARTITION of
    employees_range for values from ('2001-01-01') to ('2002-01-01');


insert into employees_range (birth_date, country_code) values
('2000-01-01','US'),
('2000-01-02','US'),
('2000-12-31','US'),
('2000-01-01','US'),
('2001-01-01','US'),
('2001-01-02','US'),
('2001-12-31','US'),
('2001-01-01','US');

select * from employees_range;
select * from only employees_range; -- you will get nothing
select * from employee_range_y2000;
select * from employee_range_y2001;

explain analyze select * from employees_range 
where birth_date = '2001-01-01';
```

## List Partitioning

```sql
create table employee_list (
    id bigserial,
    birth_date DATE NOT NULL ,
    country_code VARCHAR(2) not null
) PARTITION BY LIST ( country_code );

create table employee_list_eu PARTITION of employee_list
    for values in ('UK','DE');

create table employee_list_us PARTITION of employee_list
    for values in ('US','BZ');
```

## Hash Partitioning

```sql
create table employee_hash (
    id bigserial,
    birth_date DATE NOT NULL,
    country_code varchar(2) not null
) PARTITION BY HASH (id);

create table employee_hash_0 partition of employee_hash
for values with (modulus 3, remainder 0);

create table employee_hash_1 partition of employee_hash
for values with (modulus 3, remainder 1);

create table employee_hash_2 partition of employee_hash
for values with (modulus 3, remainder 2);
```

## Default Partitioning

```sql
create table employee_default_part partition of employee_list default;
```

## Multilevel Partitioning

```sql
create table employee_list_master (
    id bigserial,
    birth_date DATE NOT NULL ,
    country_code VARCHAR(2) not null
) PARTITION BY LIST ( country_code );

create table employee_list_eu_m PARTITION of employee_list_master
    for values in ('UK','DE')
    partition by hash (id);

create table employee_list_us_m PARTITION of employee_list_master
    for values in ('US','BZ');

create table employee_hash_0_m partition of employee_list_eu_m
for values with (modulus 3, remainder 0);

create table employee_hash_1_m partition of employee_list_eu_m
for values with (modulus 3, remainder 1);

create table employee_hash_2_m partition of employee_list_eu_m
for values with (modulus 3, remainder 2);
```

## Attach and DeAttach Partitions

```sql
create table employees_list_sp partition of employee_list
 for values in ('SP');

insert into employee_list (birth_date, country_code) values ('2001-01-01','SP');

create table employees_list_in partition of employee_list
 for values in ('IN');

create table employees_list_default partition of employee_list default

ALTER TABLE employee_list detach partition employees_list_in;
```

## Altering Partitions

```sql
create table t1
(
    a int,
    b int
) partition by range (a);

create table t1p1 partition of t1 for values from (0) to (1000);
create table t1p2 partition of t1 for values from (2000) to (3000);

insert into t1 (a, b)
values (1, 1);

-- detach
-- alter
-- attach


BEGIN TRANSACTION ;
ALTER TABLE T1 DETACH PARTITION t1p1;
ALTER TABLE t1 attach partition t1p1 for values FROM (0) to (200);
commit TRANSACTION;
```

## Partition Indexes

* Creating an index on the master/parent table will automatically create same indexes to every attached table partition
* PostgreSQL doesnt allow a way to create a single index covering every partition of the parent table. You have to create indexes for each table

```sql
create unique index idx_u_employee_list_id_country_code on employee_list
    (id); -- error

create unique index idx_u_employee_list_id_country_code on employee_list
    (id,country_code); -- with paritition key
```

