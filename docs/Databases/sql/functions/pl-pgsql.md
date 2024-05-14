# PL/pgSQL

## Declaring Variables

```sql
DO
$$
    DECLARE
        mynum      integer     := 89;
        first_name varchar(20) := 'Uday';
        hire_date  date        := '2020-01-01';
        start_time timestamp   := NOW();
        emptyvar   integer;

    BEGIN
        RAISE NOTICE 'My variable % % % % %',
        mynum, first_name, hire_date,
        start_time, emptyvar ;
    END;
$$
```

## Parameters to Function

```sql

CREATE OR REPLACE FUNCTION function_name 
    (INT, INT) RETURNS INT as
$$
    DECLARE
        x alias for $1;
        y alias for $2;

    BEGIN
        --
    END;
$$
```

## Assigning value from result into variable

```sql
DO
$$
    DECLARE 
        product_title products.product_name%TYPE;
    BEGIN
        SELECT product_name FROM products 
        INTO product_title
        where product_id = 1 limit 1;

        RAISE NOTICE 'Your product name is %', product_title;
    END;
$$        
```

## Function Parameter w/t IN and OUT

```sql
CREATE OR REPLACE FUNCTION fn_sum_using_inout 
    ( IN x integer, IN y integer, OUT Z integer ) as 
$$

    BEGIN
        z := x+y;
    END;

$$
LANGUAGE PLPGSQL;

select fn_sum_using_inout(2,3);

-- another example

CREATE OR REPLACE FUNCTION fn_sum_using_inouts 
    ( IN x integer, IN y integer, OUT Z integer, OUT w integer ) as 
$$

    BEGIN
        z := x+y;
        w := x*y;
    END;

$$
LANGUAGE PLPGSQL;

select * from fn_sum_using_inouts(2,3);
```

## Nested functions

```sql
DO
$$
    << Parent >>

    DECLARE
        counter integer := 0;
    BEGIN
        counter := counter+1;
        RAISE NOTICE 'the current value of counter (IN PARENT) is %', counter;

            DECLARE 
                counter integer := 0;
            BEGIN
                counter := counter + 5;
                RAISE NOTICE 'The current value of counter at subblocks is %', counter;
                RAISE NOTICE 'The parent value of counter at subblocks is %', PARENT.counter;
            END;

        counter := counter + 5;
        RAISE NOTICE 'the current value of counter (IN PARENT) is %', counter;

    END;
$$
LANGUAGE PLPGSQL;
```

## Returning ResultSet from function

```sql
CREATE OR REPLACE FUNCTION fn_order_by_date_pro() RETURNS SETOF orders AS
$$
    BEGIN
        RETURN QUERY SELECT * FROM orders limit 10;
    END;
$$
LANGUAGE PLPGSQL;

SELECT * FROM fn_order_by_date_pro();
```

## Conditional Statement inside functions

## Default Parameters

```sql
CREATE OR REPLACE FUNCTION fn_which_is_greater
    ( x integer default 0, y integer default 0 ) RETURNS text AS
$$
    BEGIN
        IF x > y then 
            return ' x > y ';
        else 
            return ' x < y ';
        end if ;
    END;
$$ LANGUAGE PLPGSQL;

SELECT  fn_which_is_greater(4,3);
```

## Switch Case Example

```sql
CREATE OR REPLACE FUNCTION fn_checker 
    ( x integer default 0 ) RETURNS text AS
$$
    BEGIN
        CASE x
            when 10 then
                return 'value = 10';
            when 20 then
                return 'value = 20';
            else
                RETURN 'MORE';
        END CASE;
    END;
$$ LANGUAGE PLPGSQL;

SELECT fn_checker(30);
```

## Loops in PLPGSQL

```sql
DO
$$
    DECLARE 
        i_counter integer = 0;
    BEGIN
        LOOP
            RAISE NOTICE '%', i_counter;
            i_counter := i_counter+1;
            EXIT WHEN
                i_counter = 5;
        END LOOP;
    END;
$$ LANGUAGE PLPGSQL;
```

## Loops in range exaple

```sql
DO
$$
    BEGIN
        FOR counter IN 1..5 BY 1
        LOOP

            RAISE NOTICE 'COUNTER : %', counter;
        END LOOP;        
    END;
$$
LANGUAGE PLPGSQL;
```

## Reverse Loops

```sql
DO
$$
    BEGIN
        FOR counter IN REVERSE 5..1 BY 1
        LOOP
            RAISE NOTICE 'COUNTER : %', counter;
        END LOOP;        
    END;
$$
LANGUAGE PLPGSQL;
```

## Iterating over result set

```sql
DO
$$
    DECLARE 
        rec record ;
    BEGIN
        FOR rec in 
            select order_id, customer_id from orders LIMIT 10
        LOOP
            RAISE NOTICE '% %', rec.order_id, rec.customer_id;
        END LOOP; 
    END;
$$ LANGUAGE PLPGSQL
```

## Loop with Exit condition

```sql
DO
$$
    DECLARE 
        i_counter int = 0;
    BEGIN
        LOOP
            i_counter = i_counter + 1;
        EXIT WHEN
            i_counter > 20;
        CONTINUE
            WHEN MOD (i_counter,2) = 0;

        RAISE NOTICE 'COUNTER : %', i_counter;
        END LOOP;
    END;
$$ LANGUAGE PLPGSQL;
```

## Declaring arrays in PLPGSQL

```sql
DO
$$
    DECLARE 
        arr1 int[] := array[1,2,3];
        arr2 int[] := array[4,5,6,7,8];
        var int;
    BEGIN
        FOREACH var IN ARRAY arr1||ARR2
        LOOP
            RAISE NOTICE '%', var;
        END LOOP;
    END;
$$ LANGUAGE PLPGSQL;
```

## While Loop in PLPGSQL

```sql
CREATE OR REPLACE FUNCTION fn_while_loop_sum_all(x integer) 
    returns numeric as
$$
    DECLARE 
        counter integer := 1;
        sum_all integer := 0;
    BEGIN
        WHILE counter <= x
        LOOP
            sum_all := sum_all + counter;
            counter := counter + 1;
        END LOOP;
        return sum_all;
    END;
$$ language plpgsql

select fn_while_loop_sum_all(4);
```

## Returning specific Query from column

```sql
CREATE OR REPLACE FUNCTION fn_api_products_by_names(p_pattern varchar)
RETURNS TABLE ( productname varchar, unitprice real )
AS
$$
    BEGIN
        RETURN QUERY
            SELECT product_name,unit_price from products 
            where product_name like p_pattern;

    END;
$$ LANGUAGE PLPGSQL;

SELECT * FROM fn_api_products_by_names('A%');
```

```sql
CREATE OR REPLACE FUNCTION fn_all_orders_greater() RETURNS SETOF order_details as 
$$
    DECLARE
        r record;
    BEGIN
        for r in
            select * from order_details where unit_price > 100
        loop
            return next r;
        end loop;
        return;
    end;
$$ language plpgsql;

select * from fn_all_orders_greater();
```

## If data not found condition

```sql
DO
$$
    DECLARE 
        rec record;
        orderid smallint = 1;
    BEGIN
        SELECT customer_id, order_date
        FROM orders 
        INTO STRICT rec
        WHERE order_id = orderid;

        EXCEPTION
            WHEN NO_DATA_FOUND THEN
                RAISE EXCEPTION 'No order id was found';

    END;
$$ LANGUAGE PLPGSQL;    
```

## Throwing execption on condition

```sql
DO
$$

    DECLARE 
        rec record;
        orderid smallint = 1;
    BEGIN
        SELECT customer_id, order_date
        FROM orders 
        INTO STRICT rec
        WHERE order_id > 1000;

        EXCEPTION
            WHEN TOO_MANY_ROWS THEN
                RAISE EXCEPTION 'Too many rows were found';

    END;
$$ LANGUAGE PLPGSQL;  
```

## Throwing execption example

```sql
CREATE OR REPLACE FUNCTION fn_div_exception (x real, y real) RETURNS real as 
$$

    DECLARE 
        ret real;
    BEGIN
        ret := x / y;
        return ret;
    EXCEPTION
        WHEN division_by_zero then
            RAISE INFO 'division by zero error';
            RAISE INFO 'ERROR % %', SQLSTATE, SQLERRM;
    END;
$$ LANGUAGE PLPGSQL;

SELECT fn_div_exception(5,0);
```
