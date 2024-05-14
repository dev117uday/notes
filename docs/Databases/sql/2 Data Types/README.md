
# Data Types

## Data Types

### Boolean Data

* TRUE
* FALSE
* NULL

| TRUE | FALSE |
| :--- | :--- |
| TRUE | FALSE |
| 'true' | 'false' |
| 't' | 'f' |
| 'yes' | 'no' |
| 'y' | 'n' |
| '1' | '0' |

```sql
CREATE TABLE booltable (
    id SERIAL PRIMARY KEY ,
    is_enable BOOLEAN NOT NULL
);

INSERT INTO booltable (is_enable) VALUES (TRUE), ('true'), 
    ('y') , ('yes'), ('t'), ('1');
INSERT INTO booltable (is_enable) VALUES (FALSE), ('false'), 
    ('n') , ('no'), ('f'), ('0');

SELECT * FROM booltable;

SELECT * FROM booltable WHERE is_enable = 'y';

SELECT * FROM booltable WHERE NOT is_enable;
```

### Character Data

| Character Type | Notes |
| :--- | :--- |
| CHARACTER \(N\), CHAR \(N\) | fixed-length, blank padded |
| CHARACTER VARYING \(N\), VARCHAR\(N\) | variable length with length limit |
| TEXT, VARCHAR | variable unlimited length, max 1GB |

* n is default to 1

```sql
-- INPUT
SELECT CAST('Uday' as character(10)) as "name";
-- OUTPUT
"Uday      "

-- INPUT
SELECT 'Uday'::character(10) as "name";
-- OUTPUT
"Uday      "

-- INPUT
SELECT 'uday'::varchar(10);
-- OUTPUT
"uday"

-- INPUT
SELECT 'lorem ipsum'::text;
-- OUTPUT
"lorem ipsum"
```

### Numeric Data

| Types | Notes |
| :--- | :--- |
| Integers | whole number, +ve and -ve |
| Fixed-point, floating point | for fractions of whole nu |

| type | size \(bytes\) | min | max |
| :--- | :--- | :--- | :--- |
| smallint | 2 | -32678 | 32767 |
| integer | 4 | -2,147,483,648 | 2,147,483,647 |
| bigint | 8 | -9223372036854775808 | 9223372036854775807 |

| type | size | range |
| :--- | :--- | :--- |
| smallserial | 2 | 1 to 32767 |
| serial | 4 | 1 to 2147483647 |
| bigserial | 8 | 1 to 9223372036854775807 |

### Fixed Point Data

**numeric \( precision , scale \) \| decimal \( precision , scale \)**

* precision : max number of digits to the left and right of the decimal point
* scale : number of digits allowable on the right of the decimal point

### Floating Point Data

| Type | Notes |
| :--- | :--- |
| Real | allows precision to six decimal digits |
| Double precision | allows precision to 15 digits points of precision |

| type | size | storage type | Range |
| :--- | :--- | :--- | :--- |
| numeric, decimal | variable | fixed point | 131072 digits before decimal point and 16383 digits after the decimal point |
| real | 4 | floating point | 6 decimal digits precision |
| double precision | 8 | floating point | 15 decimal digits precision |

```sql
CREATE TABLE table_numbers (
    col_numeric numeric(20,5),
    col_real real,
    col_double double precision
);

INSERT INTO table_numbers (col_numeric,col_real,col_double)
VALUES (.9,.9,.9),
       (3.34675,3.34675,3.34675),
       (4.2345678910,4.2345678910,4.2345678910);

SELECT * FROM table_numbers;

-- OUTPUT
learning=# select * from table_numbers ;
 col_numeric | col_real | col_double  
-------------+----------+-------------
     0.90000 |      0.9 |         0.9
     3.34675 |  3.34675 |     3.34675
     4.23457 | 4.234568 | 4.234567891
(3 rows)
```

**Hierarchical order to SELECT best type : numeric &gt; decimal &gt; float**

### Date Time Data

| type | stores | low | high |
| :--- | :--- | :--- | :--- |
| Date | date only | 4713 BC | 294276 AD |
| Time | time only | 4713 BC | 5874897 AD |
| Timestamp | date and time | 4713 BC | 294276 AD |
| `Timestampz` | date, time and timezone | 4713 BC | 294276 AD |
| Interval | difference btw time |  |  |

#### Date type

```sql
CREATE TABLE table_dates (
    id serial primary key,
    employee_name varchar(100) not null,
    hire_date DATE NOT NULL,
    add_date DATE DEFAULT CURRENT_DATE
);

INSERT INTO table_dates (employee_name, hire_date)
    VALUES ('uday','2020-02-02'),('another uday','2020-02-01');

SELECT *
FROM table_dates;

SELECT NOW();
```

#### Time type

```sql
CREATE TABLE table_time (
    id serial primary key ,
    class_name varchar(10) not null ,
    start_time time not null ,
    end_time time not null
);

INSERT INTO table_time (class_name, start_time, end_time) 
    VALUES ('maths','08:00:00','08:55:00'),
           ('chemistry','08:55:00','09:00:00');

SELECT * FROM table_time;

-- OUTPUT

 id | class_name | start_time | end_time 
----+------------+------------+----------
  1 | maths      | 08:00:00   | 08:55:00
  2 | chemistry  | 08:55:00   | 09:00:00
(2 rows)


SELECT CURRENT_TIME;

    current_time    
--------------------
 07:21:00.163354+00
(1 row)


SELECT CURRENT_TIME(2);

  current_time  
----------------
 07:21:14.96+00
(1 row)


SELECT LOCALTIME;

    localtime    
-----------------
 07:21:36.717509
(1 row)


SELECT time '12:10' - time '04:30' as RESULT;
  result  
----------
 07:40:00
(1 row)


-- format : interval 'n type'
-- n = number
-- type : second, minute, hours, day, month, year ....

SELECT CURRENT_TIME ,
    CURRENT_TIME + INTERVAL '2 hours' as RESULT;

    current_time    |       result       
--------------------+--------------------
 07:22:06.241919+00 | 09:22:06.241919+00
(1 row)


SELECT CURRENT_TIME ,
    CURRENT_TIME + INTERVAL '-2 hours' as RESULT;

    current_time    |       result       
--------------------+--------------------
 07:22:16.644727+00 | 05:22:16.644727+00
(1 row)
```

#### Timestamp and Timezone

* `timestamp` : stores time without time zone
* `timestamptz` : timestamp with time zone , stored using UTC format
* adding timestamp to timestamptz without mentioning the zone will result in server automatically assumes timezone to system's timezone
* **Internally, PostgreSQL will store the timezoneaccurately but then OUTPUTting the data, will it be converted according to your timezone**

```sql
SELECT name FROM pg_timezone_names 
    where name = 'posix/Asia/Calcutta';

SET TIMEZONE='Asia/Calcutta';

SELECT NOW()::TIMESTAMP;

            now             
----------------------------
 2021-08-12 12:53:03.971433
(1 row)


CREATE TABLE table_time_tz (
    ts timestamp,
    tstz timestamptz
);

INSERT INTO table_time_tz (ts, tstz) 
    VALUES ('2020-12-22 10:10:10',
            '2020-12-22 10:10:10.009+05:30');

SELECT * FROM table_time_tz;

         ts          |             tstz              
---------------------+-------------------------------
 2020-12-22 10:10:10 | 2020-12-22 10:10:10.009+05:30
(1 row)


SELECT CURRENT_TIMESTAMP;

        current_timestamp        
---------------------------------
 2021-08-12 12:53:29.54762+05:30
(1 row)


SELECT timezone('Asia/Singapore','2020-01-01 00:00:00')

      timezone       
---------------------
 2020-01-01 02:30:00
(1 row)
```

### UUID

* UUID : Universal Unique Identifier
* PostgreSQL doesn't provide internal function to generate UUID's, use `uuid-ossp`

```sql
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

SELECT uuid_generate_v1();

           uuid_generate_v1           
--------------------------------------
 4d459e0c-fb3e-11eb-a638-0242ac110002


-- pure randomness
SELECT uuid_generate_v4();

           uuid_generate_v4           
--------------------------------------
 418f39e5-8a46-4da2-8cea-884904f45d6f


CREATE TABLE products_uuid (
    id uuid default uuid_generate_v1(),
    product_name varchar(100) not null
);

INSERT INTO products_uuid (product_name) 
    VALUES ('ice cream'),('cake'),('candies');

SELECT * FROM products_uuid;

                  id                  | product_name 
--------------------------------------+--------------
 5cf1dbe0-fb3e-11eb-a638-0242ac110002 | ice cream
 5cf1df28-fb3e-11eb-a638-0242ac110002 | cake
 5cf1df46-fb3e-11eb-a638-0242ac110002 | candies

CREATE TABLE products_uuid_v4 (
    id uuid default uuid_generate_v4(),
    product_name varchar(100) not null
);

INSERT INTO products_uuid_v4 (product_name) 
    VALUES ('ice cream'),('cake'),('candies');

SELECT * FROM products_uuid_v4;

learning=# SELECT * FROM products_uuid_v4;
                  id                  | product_name 
--------------------------------------+--------------
 83b74bed-2cf8-4e26-80b0-c7c7b2e5f3e7 | ice cream
 ac563251-7a95-408d-966b-ed5ecc1f228d | cake
 1079f6d3-b0c3-40ef-bd2e-da4467b63432 | candies
```

### HSTORE

* stores data in key-value pairs
* key and VALUES are text string only

```sql
CREATE EXTENSION IF NOT EXISTS hstore;

CREATE TABLE table_hstore (
    id SERIAL PRIMARY KEY ,
    title varchar(100) not null,
    book_info hstore
);

INSERT INTO table_hstore (title, book_info) VALUES
(
    'Title 1', ' "publisher" => "ABC publisher" , 
    "paper_cost" => "100" , "e_cost" => "5.85" '
);

SELECT * FROM table_hstore;

 id |  title  |   book_info                              

  1 | Title 1 | "e_cost"=>"5.85", "publisher"=>"ABC publisher", "paper_cost"=>"100"


SELECT book_info -> 'publisher' as publisher 
FROM table_hstore;

   publisher   
---------------
 ABC publisher
```

### Json

* PostgreSQL supports both 
  * JSON
  * BSON or JSONB \( Binary JSON \)
* JSONB has full support for indexing

```sql
CREATE TABLE table_json (
    id SERIAL PRIMARY KEY ,
    docs json
);

INSERT INTO table_json (docs) 
    VALUES ('[1,2,3,4,5,6]'),('{"key":"value"}');

INSERT INTO table_json (docs)
VALUES ('[{"key":"value"},{"key2":"value2"}]');

SELECT * FROM table_json;

 id |                docs                 
----+-------------------------------------
  1 | [1,2,3,4,5,6]
  2 | {"key":"value"}
  3 | [{"key":"value"},{"key2":"value2"}]


ALTER TABLE table_json alter column docs type jsonb;

SELECT * FROM table_json where docs @> '2';

 id |        docs        
----+--------------------
  1 | [1, 2, 3, 4, 5, 6]


CREATE index on table_json USING GIN (docs jsonb_path_ops);
```

### Network Address Data Types

| Name | Storage Size | Notes |
| :--- | :--- | :--- |
| cidr | 7 or 19 bytes | IPv4 and IPv6 networks |
| inet | 7 or 19 bytes | IPv4 and IPv6 hosts and networks |
| macaddr | 6 bytes | MAC addresses |
| macaddr8 | 8 bytes | MAC addresses \( EUI 64-bit \) |

* It is better to use these types instead of plain text types of store network address, because  these types offer input error checking and specialised operators and functions
* Supports indexing and advance operations

```sql
CREATE TABLE table_netaddr (
    id SERIAL PRIMARY KEY ,
    ip inet
);

INSERT INTO table_netaddr (ip)
VALUES ('148.77.50.74'),
        ('110.158.172.66'),
        ('176.103.251.175'),
        ('84.84.14.58'),
        ('141.122.225.161'),
        ('78.44.113.33'),
        ('81.236.254.9'),
        ('82.116.85.21'),
        ('54.64.79.223'),
        ('162.240.78.253');

SELECT * FROM table_netaddr LIMIT 5;

 id |       ip        
----+-----------------
  1 | 148.77.50.74
  2 | 110.158.172.66
  3 | 176.103.251.175
  4 | 84.84.14.58
  5 | 141.122.225.161


SELECT 
       ip, 
       set_masklen(ip,24) as inet_24, 
       set_masklen(ip::cidr,24) as cidr_24 ,
       set_masklen(ip::cidr,27) as cidr_27,
       set_masklen(ip::cidr,28) as cidr_28 
FROM 
     table_netaddr LIMIT 2;

 ip | inet_24 | cidr_24 | cidr_27 | cidr_28 

 148.77.50.74   | 148.77.50.74/24   | 148.77.50.0/24   | 148.77.50.64/27   | 148.77.50.64/28
 110.158.172.66 | 110.158.172.66/24 | 110.158.172.0/24 | 110.158.172.64/27 | 110.158.172.64/28
```

