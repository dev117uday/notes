# CQL

### Create KeySpace&#x20;

( same as db in SQL )

```sql
CREATE KEYSPACE mykeyspace WITH REPLICATION = { 
		'class' : 'NetworkTopologyStrategy', 
		'replication_factor' : 3
};
	
use mykeyspace;

DESCRIBE mykeyspace;

CREATE TABLE users ( 
	user_id int, 
	fname text, 
	lname text, 
	PRIMARY KEY((user_id))
); 
```

### Create Table & Insert Data

```sql
CREATE TABLE users ( 
	user_id int, 
	fname text, 
	lname text, 
	PRIMARY KEY((user_id))
); 

insert into users(user_id, fname, lname) values (1, 'rick', 'sanchez'); 
insert into users(user_id, fname, lname) values (4, 'rust', 'cohle'); 

select * from users;

CONSISTENCY QUORUM | ALL
```

### More Queries

```sql
SELECT * from 
    heartrate_v1 
WHERE 
    pet_chip_id = 123e4567-e89b-12d3-a456-426655440b23;
    
DELETE FROM 
    heartrate_v1 
WHERE 
    pet_chip_id = 123e4567-e89b-12d3-a456-426655440b23;

```

All inserts in Scylla DB (and Cassandra) are actually upserts (insert/update). There can be only one set of values for each unique primary key. If we insert again with the same primary key, the values will be updated.

### Data Modelling

[https://docs.scylladb.com/stable/cql/types.html](https://docs.scylladb.com/stable/cql/types.html)

#### Map

```sql
CREATE TABLE pets_v1 (
    pet_chip_id text PRIMARY KEY,
    pet_name text,
    favorite_things map<text, text> // A map of text keys, and text values
);

INSERT INTO pets_v1 (pet_chip_id, pet_name, favorite_things)
           VALUES ('123e4567-e89b-12d3-a456-426655440b23', 
           'Rocky', { 'food' : 'Turkey', 'toy' : 'Tennis Ball' });
```

#### Set

```sql
CREATE TABLE pets_v2 (
    pet_name text PRIMARY KEY,
    address text,
    vaccinations set<text> 
);
```

```sql
INSERT INTO pets_v2 (pet_name, address, vaccinations)
            VALUES ('Rocky', '11 Columbia ave, New York NY', 
            { 'Heartworm', 'Canine Hepatitis' });
```

#### List

```sql
CREATE TABLE pets_v3 (
    pet_name text PRIMARY KEY,
    address text,
    vaccinations list<text>
);

INSERT INTO pets_v3 (pet_name, address, vaccinations)
            VALUES ('Rocky', '11 Columbia ave, New York NY',  
            ['Heartworm', 'Canine Hepatitis', 'Heartworm']);
```
