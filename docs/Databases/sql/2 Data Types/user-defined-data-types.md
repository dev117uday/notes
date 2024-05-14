---
description: yours truely
---

# User Defined Data Types

## CREATE DOMAIN

* Create user defined data type with a range, optional, DEFAULT, NOT NULL and CHECK Constraint.
* They are unique within schema scope.
* Helps standardise your database types in one place.
* Composite Type : Only single value return

```sql
CREATE DOMAIN name datatype constraint

-- ex 1
-- 'addr' with domain VARCHAR(100)
CREATE DOMAIN addr VARCHAR(100) NOT NULL;

CREATE TABLE locations (
    address addr
);

            Table "public.locations"
 Column  | Type | Collation | Nullable | Default 
---------+------+-----------+----------+---------
 address | addr |           |          | 


-- Dropping Constraints
-- if domain isnt used anywhere
drop domain addr;    

-- this will drop the column in the table it is present in
-- use this with caution
drop domain addr CASCADE;

-- List all domains inside a schema
select typname from pg_catalog.pg_type 
 join pg_catalog.pg_namespace 
 on pg_namespace.oid = pg_type.typnamespace
 where typtype = 'd' and nspname = 'public';
 
      typname      
------------------
 positive_numeric
 valid_color
 addr
```

## Number Based Components

```sql
-- Example 2
-- 'positive_numeric' : value > 0

CREATE DOMAIN positive_numeric 
    INT NOT NULL CHECK (VALUE > 0);

CREATE TABLE sample (
    number positive_numeric
);

INSERT INTO sample (NUMBER) VALUES (10);

-- error
INSERT INTO sample (NUMBER) VALUES (-10);

-- ERROR:  value for domain positive_numeric 
--    violates check constraint "positive_numeric_check"

SELECT * FROM sample;

 number 
--------
     10
```

## Text Based Domain

```sql
-- Example 3
-- check email domain

CREATE DOMAIN 
     proper_email VARCHAR(150) 
CHECK 
    ( VALUE ~* '^[A-Za-z0-9._%-]+@[A-Za-z0-9.-]+[.][A-Za-z]+$' );


CREATE TABLE email_check (
    client_email proper_email
);

insert into email_check (client_email) 
    values ('a@b.com') ;
    
-- error 
insert into email_check (client_email) 
    values ('a@#.com') ;
```

## Enum Based Domain

```sql
-- enum based domain
CREATE DOMAIN valid_color VARCHAR(10)
CHECK (VALUE IN ('red','green','blue'));

CREATE TABLE color (
    color valid_color
);

INSERT INTO color (color) 
    VALUES ('red'),('blue'),('green');

-- error
INSERT INTO color (color) 
    VALUES ('yellow');
```

## Composite Data Types

**Syntax :** `(composite_column).city`

### Example 1

```sql
-- address type

CREATE TYPE address AS (
    city VARCHAR(50),
    country VARCHAR(100)
);

CREATE TABLE person (
    id SERIAL PRIMARY KEY,
    address address
);

INSERT INTO person ( address ) 
    VALUES (ROW('London','UK')), (ROW('New York','USA'));

select * from person;

 id |     address      
----+------------------
  1 | (London,UK)
  2 | ("New York",USA)

select (address).country from person;

 country 
---------
 UK
 USA
```

### Example 2

```sql

CREATE TYPE currency AS ENUM(
    'USD','EUR','GBP','CHF'
);

SELECT 'USD'::currency

 currency 
----------
 USD
 
SELECT 'INR'::currency

-- ERROR:  invalid input value for enum currency: "INR"
-- LINE 1: SELECT 'INR'::currency

ALTER TYPE currency ADD VALUE 'CHF' AFTER 'EUR';

CREATE TABLE stocks (
    id SERIAL PRIMARY KEY,
    symbol currency
);

insert into stocks ( symbol ) VALUES ('CHF');

select * from stocks
 id | symbol 
----+--------
  1 | CHF

-- DROP TYPE currency;
```

## Alter

### Alter TYPE

```sql
ALTER TYPE addr RENAME TO user_address

ALTER TYPE user_address OWNER TO uday

ALTER TYPE user_address SET SCHEMA test_scm

ALTER TYPE test_scm.user_address 
    ADD ATTRIBUTE street_address VARCHAR(150)    

CREATE TYPE mycolors AS ENUM ('green','red','blue')

ALTER TYPE mycolors RENAME VALUE 'red' TO 'orange'

SELECT enum_range(NULL::mycolors);

ALTER TYPE mycolors ADD VALUE 'red' BEFORE 'green'
```

### ALTER ENUM

```sql
CREATE TYPE status_enum AS enum 
('queued','waiting','running','done');

CREATE TABLE jobs (
    id SERIAL PRIMARY KEY,
    job_status status_enum
);

INSERT INTO jobs ( job_status ) VALUES 
    ('queued'),('waiting'),('running'),('done');

SELECT * FROM jobs;

 id | job_status 
----+------------
  1 | queued
  2 | waiting
  3 | running
  4 | done

-- UPDATING waiting to running

UPDATE jobs SET job_status = 'running' 
    WHERE job_status = 'waiting';

 id | job_status 
----+------------
  1 | queued
  3 | running
  4 | done
  2 | running
  
```

## Updating/Replacing ENUM domain

```sql
ALTER TYPE status_enum RENAME TO status_enum_old;

CREATE TYPE status_enum as enum 
    ('queued','running','done');

ALTER TABLE jobs ALTER COLUMN job_status 
    TYPE status_enum USING job_status::text::status_enum;

DROP TYPE status_enum_old;
```

## Default value ENUM

```sql
CREATE TYPE  status AS ENUM 
    ('PENDING','APPROVED','DECLINE')

CREATE TABLE cron_jobs (
    id SERIAL,
    status status DEFAULT 'PENDING'
);

INSERT INTO cron_jobs ( status ) VALUES ('APPROVED');
```

## CREATE DOMAIN IF NOT EXISTS

```sql
DO
$$
BEGIN
    IF NOT EXISTS ( 
            SELECT 
                * 
            FROM pg_type tp 
            INNER JOIN 
                pg_namespace nsp ON nsp.oid = typ.typnamespace
                WHERE nsp.nspname = current_schema() 
                AND typ.typname = 'a' 
            ) 
        THEN
            CREATE TYPE ai AS ( 
                a TEXT, i INT 
            );
    END IF;
END;
$$
LANGUAGE plpgsql;
```

