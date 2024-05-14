
# Advance Tables

### Generated Columns

* faster than triggers

```sql
create table if not exists area (
    w real,
    h real,
    area real GENERATED ALWAYS AS ( w*h ) STORED
);

INSERT INTO area (w, h) values (2,3),(4,7);

select * from area;

update area
set w = 10
where w = 4

select * from area;

```



# Internals

```sql
-- size of database 
SELECT 
	datname as db_name,
	pg_size_pretty(pg_database_size(datname)) 
		as database_size
FROM
	pg_database
ORDER BY
	pg_database_size(datname) DESC;
	
-- list all databases and schema
SELECT 
	catalog_name as "Database Name"
FROM 
	information_schema.information_schema_catalog_name;

-- list all schemas
SELECT 
	catalog_name, schema_name, schema_owner
FROM 
	information_schema.schemata;

-- list all schema starting with pg_...
SELECT *
FROM information_schema.schemata
WHERE schema_name LIKE 'pg%';

-- list all tables
SELECT * 
FROM  information_schema.tables
WHERE table_schema = 'public'

-- list all views
SELECT * 
FROM  information_schema.views
WHERE table_schema = 'public'

-- views from information_schema
SELECT * 
FROM  information_schema.views
WHERE table_schema = 'information_schema'

-- list all columns
SELECT *
FROM information_schema.columns
WHERE table_name = 'orders'

-- look at system metadata
SELECT 
	CURRENT_CATALOG,
	CURRENT_DATABASE(),
	CURRENT_SCHEMA,
	CURRENT_USER,
	SESSION_USER;

-- LOOK AT DATABASE VERSION
SELECT VERSION();

SELECT
	has_database_privilege('learning','CREATE')
	has_schema_privilege('public','USAGE'),
	has_table_privilege('orders','INSERT'),
	has_any_column_privilege('orders','SELECT');
	
SELECT 	current_setting('timezone');

-- show all running queries
SELECT	
	pid,
	age(clock_timestamp(),query_start),
	usename as run_by_user_name,
	query as running
FROM pg_stat_activity
WHERE query != '<IDLE>'
	AND query NOT ILIKE '%pg_stat_activity%'
ORDER BY query_start DESC;

-- show all idle query
SELECT	
	pid,
	age(clock_timestamp(),query_start),
	usename as run_by_user_name,
	query as running
FROM pg_stat_activity
WHERE query = '<IDLE>'
ORDER BY query_start DESC;

-- KILL running query
SELECT pg_cancel_backend(pid);

-- get live and dead rows in table
SELECT
	relname,
	n_live_tup,
	n_dead_tup
FROM pg_stat_user_tables;

-- show location of postgres data directory
show data_directory;

-- show files of a table is located
SELECT pg_relation_filepath('orders');
```

