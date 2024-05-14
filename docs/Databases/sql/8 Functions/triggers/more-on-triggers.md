# More on Triggers

## Getting Internal Information

```sql

CREATE OR REPLACE FUNCTION fn_trigger_variables_display()
RETURNS TRIGGER
LANGUAGE PLPGSQL
AS
$$

	BEGIN

		RAISE NOTICE 'TG_NAME: %', TG_NAME;
		RAISE NOTICE 'TG_RELNAME: %', TG_RELNAME;
		RAISE NOTICE 'TG_TABLE_SCHEMA: %', TG_TABLE_SCHEMA;
		RAISE NOTICE 'TG_WHEN: %', TG_WHEN;
		RAISE NOTICE 'TG_LEVEL: %', TG_LEVEL;
		RAISE NOTICE 'TG_OP: %', TG_OP;
		RAISE NOTICE 'TG_NARGS: %', TG_NARGS;
		RAISE NOTICE 'TG_ARGV: %', TG_NAME;

		RETURN NEW;
	END;
$$

CREATE TRIGGER trg_trigger_variables_display
	AFTER INSERT
	ON t_temperature_log
	FOR EACH ROW
	EXECUTE PROCEDURE fn_trigger_variables_display();

INSERT INTO t_temperature_log  ( add_date, temperature ) values ('2020-02-02', -40);
```

## Disallow DELETE on table

```sql

-- create sample table

create table test_delete (
	id int
);

insert into test_delete (id ) values (1),(2),(3);

select * from test_delete;

-- creating function

create or replace function fn_generic_cancel_op()
returns trigger
language plpgsql
as
$$
	begin
	
	if TG_WHEN = 'AFTER' THEN
		raise exception 'you are not allowed to % rows in %.%', tg_op,tg_table_schema,tg_table_name;
	end if;
	
	raise notice '% on rows in %.% wont happen', tg_op, tg_table_schema, tg_table_name;
	return null; 
	
	end;
$$

-- creating trigger : AFTER

CREATE TRIGGER trg_disallow_delete
AFTER DELETE
ON test_delete
FOR EACH ROW
EXECUTE PROCEDURE fn_generic_cancel_op();

delete from test_delete where id = 1;

-- creating trigger : BEFORE

CREATE TRIGGER trg_disallow_delete_before
BEFORE DELETE
ON test_delete
FOR EACH ROW
EXECUTE PROCEDURE fn_generic_cancel_op();

delete from test_delete where id = 1;

-- checking

select * from test_delete;
```

## Disallow truncating

```sql
CREATE TRIGGER trg_disallow_truncate_after
AFTER TRUNCATE
ON test_delete
FOR EACH STATEMENT
EXECUTE PROCEDURE fn_generic_cancel_op();


CREATE TRIGGER trg_disallow_truncate_beforE
BEFORE TRUNCATE
ON test_delete
FOR EACH STATEMENT
EXECUTE PROCEDURE fn_generic_cancel_op();
```

## Creating Audit Trigger

- To log data changes to tables in a consistent and transparent manner.

```sql
CREATE TABLE  audit (
    id INT
);

CREATE TABLE audit_log (
    username TEXT,
    add_time TIMESTAMP,
    table_name TEXT,
    operation TEXT,
    row_before JSON,
    row_after JSON
);
```

- Please not that new OLD are not null for DELETE and INSERT triggers.

```sql
CREATE OR REPLACE FUNCTION fn_audit_trigger()
RETURNS TRIGGER 
LANGUAGE PLPGSQL
AS
$$
BEGIN

    DECLARE
        old_row json = NULL;
        new_row json = NULL;

    BEGIN

        IF TG_OP IN ('UPDATE','DELETE') THEN

            old_row = row_to_json(OLD);


        END IF;

        IF TG_OP IN ('INSERT','UPDATE') THEN

            new_row = row_to_json(NEW);

        END IF;
    
        INSERT INTO audit_log 
            ( username, add_time, table_name, operation, row_before, row_after )
        values
            (
                session_user,
                NOW(),
                TG_TABLE_SCHEMA || '.' || TG_TABLE_NAME,
                TG_OP,
                old_row,
                new_row
            );
        
        RETURN NEW;
    END;    

END;
$$

-- bind trigger

CREATE TRIGGER trg_audit_trigger
AFTER INSERT OR UPDATE OR DELETE
ON audit
FOR EACH ROW
EXECUTE PROCEDURE fn_audit_trigger();

-- Queries

insert into audit(id) values (1),(2),(3);

update audit
set id = '100'
where id = 1;

delete from audit
where id = 2;

select * from audit;
select * from audit_log;

```

## Creating Conditional Triggers

- Created by using generic WHEN clause.
- With a WHEN clause, you can write some conditions except a subquery

```sql

-- sample table

CREATE TABLE mytask (
    task_id SERIAL PRIMARY KEY,
    task text
);

-- trigger function

CREATE OR REPLACE FUNCTION fn_cancel_with_message()
RETURNS TRIGGER 
LANGUAGE PLPGSQL
AS
$$
BEGIN

    RAISE EXCEPTION '%', TG_ARGV[0];
    
    RETURN NULL;

END;
$$

-- function binding to trigger

CREATE TRIGGER trg_no_update 
BEFORE INSERT OR UPDATE OR DELETE OR TRUNCATE
ON mytask
FOR EACH STATEMENT
WHEN 
(
	EXTRACT ('DOW' FROM CURRENT_TIMESTAMP) = 5
	-- 5 means friday
	AND CURRENT_TIME > '12:00'
)
EXECUTE PROCEDURE fn_cancel_with_message('NO UPDATE ARE ALLOWED');

-- Queries

INSERT INTO mytask (task) values ('task 1'), ('task 2'), ('task 3');
```

## Disallow updating Primary Key of table

```sql
create table pg_table(
	id serial primary key,
	t text
);

insert into pg_table(t) values ('t1'),('t2');

create trigger disallow_pk_change
after update of id
on pg_table
for each row
execute procedure fn_generic_cancel_op();

update pg_table 
set id = 10
where id = 1;
```

## Event Triggers

- Event triggers are data-specific and not bind or attached to a table
- Unlike regular triggers they capture system level DLL events
- Event triggers can be BEFORE or AFTER triggers
- Trigger function can be written in any language except SQL
- Event triggers are disabled in the single user mode and can only be created by a superuser
- Syntax : `CREATE EVENT TRIGGER trg_name`

- Before creating an event trigger, we must have a function that the trigger will execute
- The function must return a specifi type called `EVENT_TRIGGER`
- This function need not (and may not) return a valuel the return type serivces merely as s signal that the function is to be invoked as an event trigger.

- Can we create conditional event trigger ? Yes, using the when clause
- Event trigger cannot be executed in an aborted transaction

### Event trigger events

|when     |explaination  |
|---------|---------|
|ddl_command_start  | This event occurs jsut BEFORE a CREATE, ALTER, or DROP DLL command is executed        |
|ddl_command_end    | This event occurs just AFTER a create, alter, or drop command has finished executing        |
|table_rewrite      | This event occurs just before a table is re written by some action of the commands `ALTER TABLE` and `ALTER TYPE`.         |
|sql_drop       | This evetn occurs just before the ddl_command_end eevent for the commands that frop database objects         |


### Event trigger variables

- `TG_TAG` :  this variable contains the 'TAG' or the command for which the trigger is executed.
- `TG_EVENT` : This variable contains the event name, which can be ddl_command_start, ddl_comman_end, and sql_drop.

### Creating an auditing event trigger

```sql
CREATE TABLE audit_dll (
	audit_ddl_id SERIAL PRIMARY KEY,
	username TEXT,
	ddl_event TEXT,
	ddl_command TEXT,
	ddl_add_time TIMESTAMPTZ
);

CREATE OR REPLACE FUNCTION fn_event_audit_ddl()
RETURNS EVENT_TRIGGER
LANGUAGE PLPGSQL
SECURITY DEFINER 
AS
$$
	BEGIN
		
		INSERT INTO public.audit_dll
		(username, ddl_event, ddl_command, ddl_add_time)
		VALUES 
		(session_user, TG_EVENT, TG_TAG, NOW());		
		
		RAISE NOTICE 'DDL activity is created';
		
	END;
$$

-- without condition
create event trigger trg_event_audit_ddl_no_cond
on ddl_command_start
execute procedure fn_event_audit_ddl();

-- with condition

create event trigger trg_event_audit_ddl
on ddl_command_start
when
 TAG IN ('CREATE TABLE')
execute procedure fn_event_audit_ddl();
```

## Dont allow anyone to create table between time

```sql
CREATE OR REPLACE FUNCTION fn_event_abort_create_table_func()
RETURNS EVENT_TRIGGER
LANGUAGE PLPGSQL
SECURITY DEFINER
AS
$$
	DECLARE
		current_hour int = EXTRACT ('HOUR' FROM NOW());
	BEGIN
		IF current_hour between 4 and 16 then
			RAISE EXCEPTION 'tables are not allowed to be created during 9-4';
		END IF;
	END;
$$

CREATE EVENT TRIGGER trg_event_create_table_function
ON ddl_command_start 
WHEN
	TAG IN ('CREATE TABLE')
EXECUTE PROCEDURE fn_event_abort_create_table_func();
```

## Dropping event trigger

```sql
drop event trigger trg_name;
```
