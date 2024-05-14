# Schema

* PostgreSQL schema's should be unique and different from each other
* Allows you to organise database objects
* Schema allow multiple users to interact with one database without interfering with each other
* Allow access and limit database objects to be accessed by the user.

```sql
-- syntax to create new schema
CREATE SCHEMA sales;

-- syntax to create new schema
CREATE SCHEMA hr;

-- syntax to rename existing schema
ALTER SCHEMA sales RENAME TO marketing;

-- drop schema, DO THIS CAREFULLY !!
DROP SCHEMA hr;

-- specify the schema for the table explicitly
select * from hr.public.jobs;

-- creating a sample table
CREATE TABLE temporders ( id SERIAL PRIMARY KEY );

-- moving the table from one schema to another
ALTER TABLE public.temporders SET SCHEMA marketing;

-- show current schema
select current_schema();

-- get default search path where the query will start looking
show search_path;
   search_path   
-----------------
 "$user", public

-- order to search path is important 
SET search_path to '$user', marketing, public;
```

## PG\_CATALOG

* PostgreSQL stores the metadata information about the database and cluster in the schema `pg_catalog`. 
* This information is partially used by PostgreSQL itself to keep track things itself, but it also presented so external people/processes can understand whats inside the database.
* `pg_catalog` schema contains system tables and all the built-in types, functions and operators.
* `pg_catalog` is effectively part of the search path. Although if it is not named explicitly in the path then it is implicitly searched before searching the path's schema.

```sql
select * from information_schema.schemata;

 catalog_name |    schema_name     | schema_owner 
--------------+--------------------+--------------
 learning     | information_schema | postgres
 learning     | public             | postgres
 learning     | pg_catalog         | postgres
 learning     | pg_toast           | postgres
```

