# Advance Data Modelling

In ScyllaDB (and Apache Cassandra), data is divided into partitions, rows, and values, which can be identified by a partition key. Sometimes the application needs to find a value by the value of another column. Doing this efficiently without scanning all of the partitions requires indexing



There are three indexing options available in ScyllaDB:&#x20;

* Materialized Views
* Global Secondary Indexes
* Local Secondary Indexes

**Materialized Views (MV)** are a global index. When a new MV is declared, a new table is created and is distributed to the different nodes using the standard table distribution mechanisms. The new MV table can have a different primary key from the base table, allowing for faster searches on a different set of columns.

**Global Secondary Indexes** (also called “Secondary Indexes”) are another mechanism in ScyllaDB which allows efficient searches on non-partition keys by creating an index. Rather than creating an index on the entire partition key, this index is created on specific columns.

**Local Secondary Indexes** are an enhancement to Global Secondary Indexes, which allow ScyllaDB to optimize workloads where the partition key of the base table and the index are the same key. Like their global counterparts, Cassandra's local indexes are based on Materialized Views.

### Materialized Views

* Materialized Views (MV) are a global index. When a new MV is declared, a new table is created and distributed to the different nodes using the standard table distribution mechanisms. It’s scalable, just like normal tables. It is populated by a query running against the base table. It’s not possible to directly update a MV; it’s updated when the base table is updated.
* Each Materialized View is a set of rows that correspond to rows present in the underlying, or base, table specified in the materialized view’s SELECT statement.\

* Reads from a Materialized View are just as fast as regular reads from a table and just as scalable. But as expected, updates to a table with Materialized Views are slower than regular updates since these updates need to update both the original table and the Materialized View and ensure the consistency of both updates. However, doing those in the application without server help would have been even slower.

Some common use cases for MV are Indexing with denormalization, different sort orders, and filtering (pre-computed queries).

**Example :**&#x20;

```sql
CREATE TABLE buildings (
    name text,
    city text,
    built_year smallint,
    height_meters smallint,
    PRIMARY KEY (name)
);

-- materialized view
CREATE MATERIALIZED VIEW building_by_city AS
 SELECT * FROM buildings
 WHERE city IS NOT NULL
 PRIMARY KEY(city, name);
 
 -- delete queries wont work
 DELETE FROM building_by_city WHERE city='Taipei';
```

Materialized Views and Indexes : [https://university.scylladb.com/courses/data-modeling/lessons/materialized-views-secondary-indexes-and-filtering/topic/materialized-views-and-secondary-indexes-hands-on-updated/](https://university.scylladb.com/courses/data-modeling/lessons/materialized-views-secondary-indexes-and-filtering/topic/materialized-views-and-secondary-indexes-hands-on-updated/)

### Global Secondary Indexes

Secondary indexes are created for one main purpose: to allow querying by a column that is not a key. CQL tables have strict schemas that define which columns form a primary key, and fundamentally we should use these keys to extract data from the database. But, in practice, we may want to occasionally query by a different, regular column, or several of them. How to achieve that? One of the ways is to create an index on that column.&#x20;

These indexes are implemented on top of materialized views. It implies that there’s a storage overhead for creating an index – it will use another table to store the data it needs. Indexes can be global or local, and let’s find out what that means.

local secondary indexes : [https://university.scylladb.com/courses/data-modeling/lessons/materialized-views-secondary-indexes-and-filtering/topic/local-secondary-indexes-and-combining-both-types-of-indexes/](https://university.scylladb.com/courses/data-modeling/lessons/materialized-views-secondary-indexes-and-filtering/topic/local-secondary-indexes-and-combining-both-types-of-indexes/)





