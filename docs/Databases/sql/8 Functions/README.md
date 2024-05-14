# Functions

## Basic Functions

### Syntax

```sql
CREATE OR REPLACE FUNCTION function_name() RETURNS return_type as 
'
    -- SQL COMMAND 
' LANGUAGE SQL;
```

## Some Examples

```sql
-- Function to Add

CREATE OR REPLACE FUNCTION fn_my_sum( int, int ) RETURNS int as 
'
    SELECT $1 + $2;
' LANGUAGE SQL;

-- function call

SELECT fn_my_sum(1,2);

-- output

 fn_my_sum 
-----------
         3
(1 row)

--------------------------------

-- Function to printer

CREATE OR REPLACE FUNCTION fn_printer( text ) RETURNS text as 
$$
    SELECT 'Hello ' || $1 ;
$$ LANGUAGE SQL;

--function call

SELECT fn_printer( 'Uday' );

-- output

 fn_printer 
------------
 Hello Uday
(1 row)

-- Another syntax

CREATE OR REPLACE FUNCTION fn_printer( text ) RETURNS text as 
$body$
    SELECT 'Hello ' || $1 ;
$body$ 
LANGUAGE SQL;

-- function call

SELECT fn_printer( 'Uday' );

-- output
 fn_printer 
------------
 Hello Uday
(1 row)
```

## Functions with DML

```sql
-- function

CREATE OR REPLACE FUNCTION fn_employee_update_country () returns void AS
$$

    update employees 
    set country = 'n/a'
    where country is NULL
$$ 
LANGUAGE SQL

SELECT fn_employee_update_country();
```

## Function with DQL

```sql
CREATE OR REPLACE FUNCTION fn_api_order_latest() RETURNS orders AS
$$

    select *
    from orders
    order by order_date DESC
    limit 1

$$
LANGUAGE SQL;

select (fn_api_order_latest()).*;

select (fn_api_order_latest()).order_date;

select order_date(fn_api_order_latest())

-----------------------------

CREATE OR REPLACE FUNCTION fn_employee_hire_bydate( p_year integer ) returns setof employees as 
$$

    select * from employees
    where extract('YEAR' from hire_date) = p_year

$$
LANGUAGE SQL

SELECT (fn_employee_hire_bydate('1992')).*;
```

## Returning table from function

```sql

CREATE OR REPLACE FUNCTION fn_orders()
    returns table
            (
                order_id SMALLINT,
                employee_id SMALLINT
            )
    as
    $$

    select order_id, employee_id
    from orders;

    $$
LANGUAGE SQL;

SELECT (fn_orders()).*;
```

## Function with Defualt parameters

```sql
CREATE OR REPLACE FUNCTION function_name 
    ( x int default 0, y int DEFAULT 10 ) returns int as
    $$ 

    select x+y;

    $$
LANGUAGE SQL;

SELECT function_name();
```

## Dropping Function

```sql
DROP FUNCTION [ IF EXISTS ] function_name 
    ( argument_list ) ( cascade | restrict );
```
