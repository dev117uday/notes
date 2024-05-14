# Sequences

### Sequences

* Specify datatype \( `SMALLINT | INT | BIGINT` \)
* Default is `BIGINT`

### List all sequence

```sql
SELECT relname AS seq_name 
    FROM pg_class WHERE relkind = 'S';
```

```sql
CREATE SEQUENCE IF NOT EXISTS test_sequence AS bigint;

SELECT NEXTVAL('test_sequence');

 nextval 
---------
       1


SELECT CURRVAL('test_sequence');

 currval 
---------
       1

SELECT SETVAL('test_sequence',3);

 setval 
--------
      3


-- set this value after the nextval is called, 
-- check using the currval cmd
SELECT SETVAL('test_sequence',300,false);

-- CHECKING CURRENT VALUE 
SELECT CURRVAL('test_sequence');
 currval 
---------
       3


ALTER SEQUENCE test_sequence RESTART WITH 100;

SELECT NEXTVAL('test_sequence');
 nextval 
---------
     100

CREATE SEQUENCE IF NOT EXISTS test_seq3
INCREMENT 50
MINVALUE 100
MAXVALUE 1000
START WITH 150;

SELECT nextval('test_seq3');

  nextval 
---------
     150

CREATE SEQUENCE IF NOT EXISTS seq_des
INCREMENT -1
MINVALUE 1
MAXVALUE 999
START 99
NO CYCLE | CYCLE ;

SELECT nextval('seq_des');

 nextval 
---------
      99

-- DROP SEQUENCE

DROP SEQUENCE IF EXISTS seq_des;

CREATE TABLE IF NOT EXISTS table_seq (
    id INT primary key ,
    name VARCHAR(10)
);

CREATE sequence IF NOT EXISTS table_seq_id_seq
start with 1 owned BY table_seq.id;

ALTER TABLE table_seq
ALTER COLUMN id SET DEFAULT nextval('table_seq_id_seq')
```

### Alpha-Numeric Sequence

```sql
CREATE sequence table_text_seq;

CREATE TABLE contacts (
    id text NOT null default ('ID' || nextval('table_text_seq')),
    name VARCHAR(150) NOT null
);

INSERT INTO  contacts (name) VALUES ('uday 1'),('uday 2'),('uday 3');

SELECT * FROM contacts;

 id  |  name  
-----+--------
 ID1 | uday 1
 ID2 | uday 2
 ID3 | uday 3
```

