# Triggers

- A postgresql trigger is a functoin invoked automatically whenever 'an event' associated with a table occurs.

- An event could be any of the following;
    - INSERT
    - UDPATE
    - DELETE
    - TRUNCATE

- You can associate a trigger with a
    - Table
    - View
    - Foreign Table

- A trigger is a special 'user-defined function'
- A trigger is automatically invoked
- We can create a triger
    - BEFORE
        - Trigger is fired before an event is about to happen
    - AFTER
        - Trigger is fired after the event is completed
    - INSTEAD
        - In case the event fails, trigger is fired

- Cannot be fired manually
- Fired in alphbetically order
- DO Not change in primary key, foriegn key or unique key column
- DO Not update records in the table that you normally read during the transaction
- DO Not read data from a table that is updating during the same transaction
- DO Not aggregate/summarized over the table that you are updating

## Types of Triggers

- Row level
    - If row is marked for `FOR EACH ROW`, then trigger will be called for each row that is getting modfied by the event

- Statement level
    - The `FOR EACH STATEMENT` will call the trigger function only ONCE for each statement, regardless of the number of rows getting modified.


| When       | Event                | Row-level | Statement-level |
| ---------- | -------------------- | --------- | --------------- |
|            | INSERT/UDPATE/DELETE | Tables    | Tables and view |
| before     | Truncate             |           |                 |
| ---------- | -------------------- | --------- | --------------- |
|            | INSERT/UDPATE/DELETE | Tables    | Tables and view |
| AFTER      | Truncate             |           |                 |
| ---------- | -------------------- | --------- | --------------- |
|            | INSERT/UDPATE/DELETE | Views     |                 |
| INSTEAD OF | Truncate             |           |                 |


## Create your own Trigger in PostgreSQL

```sql
CREATE FUNCTION trigger_function( ) 
    RETURNS TRIGGER LANGUAGE PLPGSQL
AS $$
BEGIN
    -- TRIGGER LOGIC
END;
$$
```

## Syntax

```sql
CREATE TRIGGER trigger_name {BEFORE|AFTER} {EVENT} 
ON table_name 
    [FOR [EACH] {ROW | STATEMENT}]
    EXECUTE PROCEDURE trigger_function

-- FOR EACH [ROW|STATEMENT]
```

## Data Auditing with Triggers

### Setup Example tables

```sql
CREATE TABLE players (
	player_id SERIAL PRIMARY KEY,
	name VARCHAR(100)
);

CREATE TABLE player_audits (
	player_audit_id SERIAL PRIMARY KEY,
	player_id INT NOT NULL,
	name VARCHAR(100) NOT NULL,
	new_name VARCHAR(100) NOT NULL,
	edit_date TIMESTAMP NOT NULL
);
```

### Setup Function to TRIGGER

```sql
-- Function executed by TRIGGER

CREATE OR REPLACE FUNCTION fn_players_name_changes_log()
	RETURNS TRIGGER
	LANGUAGE PLPGSQL
	AS
$$
BEGIN

	IF NEW.name <> OLD.name THEN
		INSERT INTO player_audits
		( player_id, name, new_name, edit_date ) 
		values ( OLD.player_id, OLD.name, NEW.name ,NOW() );
	END IF;

	RETURN NEW;
END;
$$

-- TRIGGER Definition

CREATE TRIGGER trg_players_name_changes
	BEFORE UPDATE 
	ON players
	FOR EACH ROW
	EXECUTE PROCEDURE fn_players_name_changes_log();
```

## DML to fire above trigger
```sql
INSERT INTO players (name) VALUES ('UDAY'),('YADAV');

SELECT * FROM players;
select * from player_audits;

UPDATE players
SET name = 'UDAY 2'
WHERE player_id = 2;
```

## Another Trigger Example

```sql

-- create table for example

CREATE TABLE t_temperature_log (
	id_temperature SERIAL PRIMARY KEY,
	add_date TIMESTAMP,
	temperature NUMERIC
);

-- create function for trigger to execute

CREATE OR REPLACE FUNCTION fn_temperature_value_check_at_insert()
RETURNS TRIGGER
LANGUAGE PLPGSQL
AS
$$
BEGIN

	IF NEW.temperature < -30 then
		NEW.temperature = 0;
	END IF;

	RETURN NEW;

END;
$$

-- creating trigger

CREATE TRIGGER trg_temperature_value_check_at_insert
BEFORE INSERT 
ON t_temperature_log
FOR EACH ROW
EXECUTE PROCEDURE fn_temperature_value_check_at_insert();

-- Queries

INSERT INTO t_temperature_log ( add_date, temperature )
values ( '2020-10-01' , 10 );

select * from t_temperature_log;

INSERT INTO t_temperature_log ( add_date, temperature )
values ( '2020-10-01' , -33 );

select * from t_temperature_log;
```
