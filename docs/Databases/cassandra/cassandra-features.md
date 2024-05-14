# Cassandra Features

* User defined Data Type : [https://university.scylladb.com/courses/data-modeling/lessons/advanced-data-modeling/topic/user-defined-types-udt/](https://university.scylladb.com/courses/data-modeling/lessons/advanced-data-modeling/topic/user-defined-types-udt/)
* Denormalization : [https://university.scylladb.com/courses/data-modeling/lessons/advanced-data-modeling/topic/denormalization/](https://university.scylladb.com/courses/data-modeling/lessons/advanced-data-modeling/topic/denormalization/)

### Time to Live

more info : [https://university.scylladb.com/courses/data-modeling/lessons/advanced-data-modeling/topic/expiring-data-with-ttl-time-to-live/](https://university.scylladb.com/courses/data-modeling/lessons/advanced-data-modeling/topic/expiring-data-with-ttl-time-to-live/)

Can be set when&#x20;

* When defining table
* insert or update operation

**Example : Insert and Update operations**

```sql
CREATE TABLE heartrate (
    pet_chip_id  uuid,
    name text,
    heart_rate int,
    PRIMARY KEY (pet_chip_id)
);

INSERT INTO heartrate(pet_chip_id, name, heart_rate) 
VALUES (123e4567-e89b-12d3-a456-426655440b23, 'Duke', 90);

-- check for ttl of heart rate
SELECT name, TTL(heart_rate)
FROM heartrate WHERE  pet_chip_id = 123e4567-e89b-12d3-a456-426655440b23;

-- set ttl for heart rate
UPDATE heartrate USING TTL 600 SET heart_rate =
110 WHERE pet_chip_id = 123e4567-e89b-12d3-a456-426655440b23;

-- for the entire row
INSERT INTO heartrate(pet_chip_id, name, heart_rate) 
VALUES (c63e71f0-936e-11ea-bb37-0242ac130002, 'Rocky', 87) USING TTL 30;
```

**Example : For Table**

```sql
CREATE TABLE heartrate_ttl (
    pet_chip_id  uuid,
    name text,
    heart_rate int,
    PRIMARY KEY (pet_chip_id))
WITH default_time_to_live = 500;
```

### Counter

more info : [https://university.scylladb.com/courses/data-modeling/lessons/advanced-data-modeling/topic/counters/](https://university.scylladb.com/courses/data-modeling/lessons/advanced-data-modeling/topic/counters/)

It’s a special data type (column) that only allows its value to be incremented, decremented, read or deleted. As a type, counters are a 64-bit signed integer. Updates to counters are atomic, making them perfect for counting and avoiding the issue of possible concurrent updates on the same value.

Counters can only be defined in a dedicated table that includes:

* The primary key (can be compound)
* The counter column

```sql
CREATE TABLE pet_type_count (
    pet_type text PRIMARY KEY, 
    pet_counter counter
);
```

**Loading data to a counter table is different than other tables, it’s done with an UPDATE operation**

```sql
UPDATE pet_type_count SET pet_counter = pet_counter + 6 
WHERE pet_type = 'dog';
```

