# Date/Time/Stamps

## Date/Time/Stamps

### Set Date Time Style

```sql
-- show system date style
SHOW datestyle;

-- set new datestyle
SET datestyle = 'ISO, DMY';
SET datestyle = 'ISO, MDY';
```

### Make

```sql
SELECT MAKE_DATE (2020,01,01);
 make_date  
------------
 2020-01-01

SELECT MAKE_DATE (2020,01,01);
 make_date  
------------
 2020-01-01

SELECT MAKE_TIME(2,3,14.65);
  make_time  
-------------
 02:03:14.65

SELECT MAKE_TIMESTAMP (2020,02,02,10,20,45.44);

    make_timestamp     
------------------------
 2020-02-02 10:20:45.44
```

### Make\_interval

```sql
SELECT MAKE_INTERVAL (2020,01,02,10,20,33);

          make_interval           
-----------------------------------
 2020 years 1 mon 24 days 20:33:00

SELECT MAKE_INTERVAL (days => 10);

make_interval 
---------------
 10 days

SELECT MAKE_INTERVAL (months => 7, days => 10, mins=>35);

     make_interval      
-------------------------
 7 mons 10 days 00:35:00

SELECT MAKE_INTERVAL (weeks => 10);

make_interval 
---------------
 70 days
```

### Make\_timestamptz

```sql
SELECT make_timestamptz(2020,02,02,10,30,45.55,'Asia/Calcutta');

     make_timestamptz      
---------------------------
 2020-02-02 05:00:45.55+00

SELECT pg_typeof(make_timestamptz(2020,02,02,10,30,45.55));

        pg_typeof         
--------------------------
 timestamp with time zone
```

### Date Value Extractor

- https://www.postgresql.org/docs/8.1/functions-datetime.html
- https://www.postgresqltutorial.com/postgresql-extract/

### Extract

```sql
select extract ('day' FROM current_timestamp), extract ('month' FROM current_timestamp), extract ('year' FROM current_timestamp);

 date_part | date_part | date_part 
-----------+-----------+-----------
        14 |         8 |      2021

select extract('epoch' FROM current_timestamp);

     date_part     
-------------------
 1628923887.158532


select extract('century' FROM current_timestamp);

 date_part 
-----------
        21
```

### Maths Operations on Date Time

```sql
select '2020-02-02'::date + 04;

  ?column?  
------------
 2020-02-06

select '23:59:59' + INTERVAL '1 SECOND';

 ?column? 
----------
 24:00:00

select '23:59:59' + INTERVAL '2 SECOND';

 ?column? 
----------
 24:00:01

SELECT CURRENT_TIMESTAMP + '01:01:01';

           ?column?            
-------------------------------
 2021-08-14 07:53:05.444791+00

SELECT DATE '20200101' + TIME '10:25:10';

      ?column?       
---------------------
 2020-01-01 10:25:10

SELECT '10:10:10' + TIME '10:25:10';

 ?column? 
----------
 20:35:20

SELECT DATE '20200101' - INTERVAL '1 HOUR';

      ?column?       
---------------------
 2019-12-31 23:00:00

SELECT INTERVAL '30 MINUTES' + '2 HOUR';

 ?column? 
----------
 02:30:00
```

### Overlap

```sql
select
    ( DATE '2020-01-01' , DATE '2020-12-31' )
    OVERLAPS
    ( DATE '2020-12-30', DATE '2020-12-01' );

 overlaps 
----------
 t
```

### Current

```sql
select
    current_date,
    current_time,
    current_time(2),
    current_timestamp;

 current_date |    current_time    |  current_time  |       current_timestamp       
 2021-08-14   | 06:53:52.187847+00 | 06:53:52.19+00 | 2021-08-14 06:53:52.187847+00


select localtime,
    localtimestamp,
    localtimestamp(2);

    localtime    |       localtimestamp       |     localtimestamp     
-----------------+----------------------------+------------------------
 06:54:07.540777 | 2021-08-14 06:54:07.540777 | 2021-08-14 06:54:07.54


select
   now(),
   transaction_timestamp(),
   clock_timestamp();

now | transaction_timestamp | clock_timestamp        

2021-08-14 06:54:31.371838+00 | 2021-08-14 06:54:31.371838+00 | 2021-08-14 06:54:31.371924+00


select statement_timestamp(),
   timeofday();

statement_timestamp      |              timeofday              
-------------------------------+-------------------------------------
 2021-08-14 06:55:07.202782+00 | Sat Aug 14 06:55:07.202849 2021 UTC
```

### Age

```sql
select age('2020-01-01', '2019-10-01');

  age   
--------
 3 mons

select age(timestamp '2020-01-01');

          age          
-----------------------
 1 year 7 mons 13 days

select age(current_date, '2020-01-01');

          age          
-----------------------
 1 year 7 mons 13 days
```

### Epochs

```sql
select age ( timestamp '2020-12-20', timestamp '2020-10-20' );

  age   
--------
 2 mons


SELECT 
    EXTRACT (EPOCH FROM TIMESTAMPTZ '2020-10-20')
    - EXTRACT (EPOCH FROM TIMESTAMPTZ '2020-08-20') 
        AS "DIFFERENCE IN SECONDS";

 DIFFERENCE IN SECONDS 
-----------------------
               5270400
```

### Timezone

```sql
SELECT * FROM pg_timezone_names;

SELECT * FROM pg_timezone_abbrevs;

SHOW TIME ZONE;

SET TIME ZONE 'Asia/Calcutta';
```

### date\_part and date\_trunc

```sql
SELECT date_part ('day', date '2021-11-07');

 date_part 
-----------
         7


SELECT date_trunc('hour', 
    timestamptz '2021-07-16 23:38:40.775719 +05:30');

       date_trunc       
------------------------
 2021-07-16 18:00:00+00
```

