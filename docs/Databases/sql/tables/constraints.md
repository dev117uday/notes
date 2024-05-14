# Constraints

* Constraints are like Gate Keepers. Control the type of data that goes into the table. These are used to prevent invalid data.
* Constraints can be added on
  * Table 
  * Column

## Types of Constraints

| Type | Note |
| :--- | :--- |
| NOT NULL | Field must have values |
| UNIQUE | Only unique value are allowed |
| DEFAULT | Ability to SET default |
| PRIMARY KEY | Uniquely identifies each row/record |
| FOREIGN KEY | Constraint based on Column in other table |
| CHECK | Check all values must meet specific criteria |

## NOT NULL

```sql
CREATE TABLE table_nn (
    id SERIAL PRIMARY KEY,
    tag text NOT NULL
);

INSERT INTO table_nn ( tag ) 
    VALUES ('TAG 1'), ('TAG 2'), ('TAG 3'), ('');

-- NULL value wont be accepted
INSERT INTO table_nn ( tag ) VALUES (NULL);

-- empty string aren't NULL values
INSERT INTO table_nn ( tag ) VALUES ('');

-- adding another column TEXT;
ALTER TABLE table_nn 
ADD COLUMN is_enable TEXT;

-- Updating values in new column to '' 
-- so that NULL constrain can be added
UPDATE table_nn
SET is_enable = '' WHERE is_enable IS NULL;

-- adding null constraint on table
ALTER TABLE public.table_nn
    ALTER COLUMN is_enable SET NOT NULL;
```

## UNIQUE

```sql
CREATE TABLE table_unique (
    id SERIAL PRIMARY KEY,
    emails TEXT UNIQUE
);

INSERT INTO table_unique ( emailS ) 
    VALUES ('A@B.COM'),('C@D.COM');

INSERT INTO table_unique ( emailS ) 
    VALUES ('A@B.COM');
-- error
```

### Adding Unique Constraint on column

```sql
ALTER TABLE public.table_unique
    ADD COLUMN is_enable text;

ALTER TABLE table_unique
    ADD CONSTRAINT unique_is_enable UNIQUE (is_enable);


ALTER TABLE public.table_unique
    ADD COLUMN code text,
    ADD COLUMN code_key text;

ALTER TABLE table_unique
    ADD CONSTRAINT unique_code_key 
        UNIQUE (code,code_key);
```

## DEFAULT CONSTRAINT

```sql
CREATE TABLE table_default (
    id SERIAL PRIMARY KEY,
    is_enable TEXT DEFAULT 'Y'
);

INSERT INTO table_default ( id ) VALUES (1),(2);

select * from table_default;

 id | is_enable 
----+-----------
  1 | Y
  2 | Y

ALTER TABLE public.table_default
    ADD COLUMN tag text;

ALTER TABLE public.table_default
    ALTER COLUMN tag SET DEFAULT 'N';

INSERT INTO table_default ( id ) VALUES (5);

select * from table_default;

 id | is_enable | tag 
----+-----------+-----
  1 | Y         | 
  2 | Y         | 
  5 | Y         | N


ALTER TABLE public.table_default
    ALTER COLUMN tag DROP DEFAULT;
    
                              Table "public.table_default"
  Column   |  Type   | Collation | Nullable |                  Default                  
-----------+---------+-----------+----------+-------------------------------------------
 id        | integer |           | not null | nextval('table_default_id_seq'::regclass)
 is_enable | text    |           |          | 'Y'::text
 tag       | text    |           |          | 
Indexes:
    "table_default_pkey" PRIMARY KEY, btree (id)

```

## Primary Key

1. Uniquely identifies each record in a database table
2. There can be more than one UNIQUE, but only one primary key
3. A primary key is a field in a table, which uniquely identifies each row/column in a database table
4. When multiple fields are used as a primary key, which may consist of single or multiple fields, also known

   as composite key.

```sql
CREATE TABLE table_item (
    item_id INTEGER PRIMARY KEY,
    item_name varchar(20) NOT NULL    
);

INSERT INTO table_item ( item_id,item_name ) 
    VALUES (1, 'pencil'), (2, 'pen' ), (3, 'box' );

INSERT INTO table_item ( item_id,item_name ) 
    VALUES (1, 'mobile');

-- ERROR:  duplicate key value violates unique constraint "table_item_pkey"
-- DETAIL:  Key (item_id)=(1) already exists.
-- SQL state: 23505

ALTER TABLE table_item
DROP CONSTRAINT table_item_pkey;

ALTER TABLE public.table_item
    ADD PRIMARY KEY (item_id);
```

## Composite Primary Key

```sql
CREATE TABLE table_cpk (
    id VARCHAR(20) NOT NULL,
    another_id VARCHAR(20) NOT NULL,
    grade_id VARCHAR(20) NOT NULL
);

INSERT INTO table_cpk ( id, another_id, grade_id ) 
    VALUES 
        ('1','11','12'), 
        ('2','21','22'), 
        ('3','31','32');

SELECT * FROM table_cpk;

-- composite key = id + another_id;

ALTER TABLE public.table_cpk
    ADD CONSTRAINT cpk_comp_pkey PRIMARY KEY (id, another_id)
```

## Foreign Key

```sql
CREATE TABLE t_suppliers (
    s_id SERIAL PRIMARY KEY,
    s_name VARCHAR(20) NOT NULL
);

CREATE TABLE t_products (
    p_id SERIAL PRIMARY KEY,
    p_name  VARCHAR(10) NOT NULL,
    s_id INT NOT NULL,
    FOREIGN KEY (s_id) REFERENCES t_suppliers (s_id)
);

INSERT INTO t_suppliers ( s_name ) 
    VALUES ('SUP 1'),('SUP 2'),('SUP 3'),('SUP 4');

INSERT INTO t_products ( P_NAME, S_ID ) 
    VALUES ('PRO 1',1),('PRO 2',2);

INSERT INTO t_products ( P_NAME, S_ID ) 
    VALUES ('PRO 1',9);

-- ERROR:  insert or update on table "t_products" 
-- violates foreign key constraint "t_products_s_id_fkey"
-- DETAIL:  Key (s_id)=(9) is not present in table "t_suppliers".
-- SQL state: 23503
```

## CHECK Constraint

```sql
CREATE TABLE table_constr (
    id SERIAL PRIMARY KEY,
    birth_date DATE CHECK ( birth_date > '1900-01-01' ),
    joined_date DATE CHECK ( joined_date > birth_date ),
    salary NUMERIC CHECK ( salary > 0 )
);

select * from table_constr;

insert into table_constr (birth_date, joined_date, salary) 
values 
    ('2001-02-02','2019-01-10',100000);

insert into table_constr (birth_date, joined_date, salary) 
values 
    ('2001-02-02','2000-01-10',100000);

insert into table_constr (birth_date, joined_date, salary) 
values 
    ('2001-02-02','2000-01-10',-100000);

ALTER TABLE public.table_constr
    ADD COLUMN prices int;

ALTER TABLE table_constr
ADD CONSTRAINT price_check 
CHECK (
    prices > 0
    AND salary > prices
);
```

## Example

```sql
CREATE TABLE web_links (
    link_id SERIAL PRIMARY KEY,
    link_url VARCHAR(255) NOT NULL,
    link_target VARCHAR(20)
);

SELECT * FROM web_links;

ALTER TABLE web_links
ADD CONSTRAINT unique_web_url UNIQUE (link_url);

INSERT INTO web_links (link_url,link_target) 
    VALUES ('https://www.google.com/','_blank');

ALTER TABLE web_links
ADD COLUMN is_enable VARCHAR(2);

INSERT INTO web_links (link_url,link_target,is_enable) 
    VALUES ('https://www.amazon.com/','_blank','Y');

ALTER TABLE web_links
ADD CHECK ( is_enable IN ('Y','N') );

INSERT INTO web_links (link_url,link_target,is_enable) 
    VALUES ('https://www.NETFLIX.com/','_blank','N');

SELECT * FROM web_links;

UPDATE web_links
SET is_enable = 'Y'
WHERE link_id = 1
```

