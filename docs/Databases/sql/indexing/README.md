
# Indexing

## What is a Index in Database

* An index help improve the access of data in our database&#x20;
* _Indexed tuple point_ to the table page where the tuple is stored on disk.
* An Index is a data structure that allows faster access to the underlying table so that specific tuples can be found quickly . Here "quickly" means much faster than scanning the entire table and analysing every single tuple.
* They add a cost to running a query, consume more memory to maintain the data structure.

```sql
INDEX        : idx_table_name_column_name 
UNIQUE INDEX : idx_u_table_name_column_name
```

## Syntax

```sql
create index index_name on table_name (col1,col2,.....)

-- create index for only unique values in column
create unique index index_name on table_name (col1,col2,.....)

-- more descriptive method
create index index_name on table_name [USING method]
(
    column_name [ASC|DESC] [NULLS {FIRST | LAST}],
    ...
);
```

```sql
create index idx_orders_order_date 
 on orders (order_date);

create index idx_orders_ship_city 
 on orders (ship_city);

create index idx_orders_customer_id_order_id 
 on orders (customer_id,order_id);

CREATE INDEX idx_shippers_company_name
    ON public.shippers USING btree
    (company_name ASC NULLS LAST);
    
                          Table "public.orders"
      Column      |         Type          | Collation | Nullable | Default 
------------------+-----------------------+-----------+----------+---------
 order_id         | smallint              |           | not null | 
 customer_id      | bpchar                |           |          | 
 employee_id      | smallint              |           |          | 
 order_date       | date                  |           |          | 
 required_date    | date                  |           |          | 
 shipped_date     | date                  |           |          | 
 ship_via         | smallint              |           |          | 
 freight          | real                  |           |          | 
 ship_name        | character varying(40) |           |          | 
 ship_address     | character varying(60) |           |          | 
 ship_city        | character varying(15) |           |          | 
 ship_region      | character varying(15) |           |          | 
 ship_postal_code | character varying(10) |           |          | 
 ship_country     | character varying(15) |           |          | 

-- indexes present on table

Indexes:
    "pk_orders" PRIMARY KEY, btree (order_id)
    "idx_orders_customer_id_order_id" btree (customer_id, order_id)
    "idx_orders_order_date" btree (order_date)
    "idx_orders_ship_city" btree (ship_city)
Foreign-key constraints:
    "fk_orders_customers" FOREIGN KEY (customer_id) 
       REFERENCES customers(customer_id)
    "fk_orders_employees" FOREIGN KEY (employee_id) 
       REFERENCES employees(employee_id)
    "fk_orders_shippers" FOREIGN KEY (ship_via) 
       REFERENCES shippers(shipper_id)
Referenced by:
    TABLE "order_details" CONSTRAINT "fk_order_details_orders" 
    FOREIGN KEY (order_id) REFERENCES orders(order_id)
```

## Get All Indexes

```sql
select * from pg_indexes limit 3;

 schemaname |  tablename   |            indexname             | tablespace |                                                           indexdef                                                            
------------+--------------+----------------------------------+------------+-------------------------------------------------------------------------------------------------------------------------------
 pg_catalog | pg_statistic | pg_statistic_relid_att_inh_index |            | CREATE UNIQUE INDEX pg_statistic_relid_att_inh_index ON pg_catalog.pg_statistic USING btree (starelid, staattnum, stainherit)
 pg_catalog | pg_type      | pg_type_oid_index                |            | CREATE UNIQUE INDEX pg_type_oid_index ON pg_catalog.pg_type USING btree (oid)
 pg_catalog | pg_type      | pg_type_typname_nsp_index        |            | CREATE UNIQUE INDEX pg_type_typname_nsp_index ON pg_catalog.pg_type USING btree (typname, typnamespace)


select * from pg_indexes where schemaname = 'public' limti 3;

  schemaname | tablename |  indexname   | tablespace |                                    indexdef                                    
------------+-----------+--------------+------------+--------------------------------------------------------------------------------
 public     | us_states | pk_usstates  |            | CREATE UNIQUE INDEX pk_usstates ON public.us_states USING btree (state_id)
 public     | customers | pk_customers |            | CREATE UNIQUE INDEX pk_customers ON public.customers USING btree (customer_id)
 public     | orders    | pk_orders    |            | CREATE UNIQUE INDEX pk_orders ON public.orders USING btree (order_id)
```

## Size of Indexes

```sql
select pg_size_pretty(pg_indexes_size('tablename'));

 pg_size_pretty 
----------------
 128 kB
```

## Stats about indexes

```sql
select * from postgres.pg_catalog.pg_stat_all_indexes;

 relid | indexrelid | schemaname |   relname    |           indexrelname           | idx_scan | idx_tup_read | idx_tup_fetch 
-------+------------+------------+--------------+----------------------------------+----------+--------------+---------------
  2619 |       2696 | pg_catalog | pg_statistic | pg_statistic_relid_att_inh_index |     1592 |         1155 |          1155
  1247 |       2703 | pg_catalog | pg_type      | pg_type_oid_index                |      924 |          924 |           911
  1247 |       2704 | pg_catalog | pg_type      | pg_type_typname_nsp_index        |      162 |          120 |           120
(3 rows)
```

## Drop Index

```sql
DROP INDEX [ concurrently ] 
[ IF EXISTS  ] INDEX_NAME  [ CASCADE | RESTRICT ];
```

* `CASCADE`: If object has dependent objects, you will also drop the dependent ones after dropping it.
* `RESTRICT` : It denies the user to drop the index if a dependency exists
* `CONCURRENTLY` : PostgreSQL will require exclusive lock over the whole table and block access until index is removed

## Vacuum analyze

\
When a vacuum process runs, the space occupied by these dead tuples is marked reusable by other tuples.&#x20;

An “analyze” operation does what its name says – it analyzes the contents of a database's tables and **collects statistics** about the distribution of values in each column of every table.

```sql
vacuum analyze table_name;
```

## Rebuilding Indexes

```sql
REINDEX ( VERBOSE ) INDEX concurrently idx_orders_ship_city;
```
