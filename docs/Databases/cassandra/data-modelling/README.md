# Data Modelling

In Scylla, as opposed to relational databases, the data model is based around the queries and not just around the domain entities. When creating the data model, we take into account both the conceptual data model and the application workflow: which queries will be performed by which users and how often.

<figure><img src="./assets/image (4).png" alt=""><figcaption></figcaption></figure>



| 1   | Query-based: Application -> Data -> Model | Entity-based: Data -> Model -> Application |
| --- | ----------------------------------------- | ------------------------------------------ |
| 4   | Denormalization                           | Support for foreign-keys, Joins            |
| 5   | CAP Theorem, Eventual Consistency         | ACID Guarantee                             |
| _6_ | Distributed Architecture                  | Mostly single point of failure             |

### Things to keep in mind when design Tables

* **Even data distribution**: data should be evenly spread across the cluster so that every **node** holds roughly the same amount of data. Scylla determines which node should store the data based on hashing the partition key. Therefore, choosing a suitable partition key is crucial. More on this later on.
* **To minimize the number of partitions accessed in a read query**: To make reads faster, we’d ideally have all the data required in a read query stored in a single Table. Although it’s fine to duplicate data across tables, in terms of performance, it’s better if the data needed for a read query is in one table.

### Things we should **NOT** focus on:

* **Avoiding data duplication**: To get efficient reads, we sometimes have to duplicate data.&#x20;
* **Minimizing the number of writes**: writes in Scylla DB aren’t free, but they are very efficient and “cheap.” Scylla DB is optimized for high write throughput. Reads, while still very fast, are usually more expensive than writes and are harder to fine-tune.&#x20;
  * We’d usually be ready to increase the number of writes to increase read efficiency. Keep in mind that the number of tables also affects consistency.&#x20;

### Important

* ScyllaDB data modeling is query-based. That is, we think of the application workflow and the queries early on in the data model process
* A Keyspace is a top-level container that stores tables
* A Table is how ScyllaDB stores data and can be thought of as a set of rows and columns
* The Primary Key is composed of the Partition Key and Clustering Key
* One of our goals in data modeling is even data distribution. For that, we need to select a partition key correctly
* Selecting the Primary Key is very important and has a huge impact on query performance

### Partition and Clustering Keys

`composite primary key = partition key + clustering key`

The **Partition Key** is responsible for data distribution across the nodes. It determines which node will store a given row. It can be **one** or more columns. Without this, query wont execute

The **Clustering Key** is responsible for sorting the rows within the partition. It can be **zero** or more column

To query without the partition key, cluster would have to perform full scan which is super expensive.

#### Example:

```sql
CREATE TABLE heartrate_v1 (
   pet_chip_id uuid,
   time timestamp,
   heart_rate int,
   PRIMARY KEY (pet_chip_id)
);

INSERT INTO heartrate_v1(pet_chip_id, time, heart_rate) 
VALUES (123e4567-e89b-12d3-a456-426655440b23, '2019-03-04 07:02:30', 130);
```

To run this query : (wont work)

```sql
SELECT * from heartrate_v1 
WHERE pet_chip_id = 123e4567-e89b-12d3-a456-426655440b23 
    AND time >='2019-03-04 07:01:00' 
    AND time <='2019-03-04 07:02:00';
```

we partition and clustering key

```sql
CREATE TABLE heartrate_v2 (
   pet_chip_id uuid,
   time timestamp,
   heart_rate int,
   PRIMARY KEY (pet_chip_id, time)
);

INSERT INTO heartrate_v2(pet_chip_id, time, heart_rate)
   VALUES (123e4567-e89b-12d3-a456-426655440b23, '2019-03-04 07:01:05', 100); 
INSERT INTO heartrate_v2(pet_chip_id, time, heart_rate) 
   VALUES (123e4567-e89b-12d3-a456-426655440b23, '2019-03-04 07:01:10', 90); 
INSERT INTO heartrate_v2(pet_chip_id, time, heart_rate) 
   VALUES (123e4567-e89b-12d3-a456-426655440b23, '2019-03-04 07:01:50', 96); 
INSERT INTO heartrate_v2(pet_chip_id, time, heart_rate) 
   VALUES (123e4567-e89b-12d3-a456-426655440b23, '2019-04-04 07:01:50', 99); 
   
SELECT * from heartrate_v2 
WHERE pet_chip_id = 123e4567-e89b-12d3-a456-426655440b23 
   AND time >='2019-03-04 07:01:00' 
   AND time <='2019-03-04 07:02:00';
```

This also eliminates the problem of storing just one value for a primary key. Partition Key can be more than one column.

**If we have multiple partition keys**

```sql
CREATE TABLE heartrate_v3 (
   pet_chip_id uuid,
   time timestamp,
   heart_rate int,
   pet_name text,
   PRIMARY KEY ((pet_chip_id, time), pet_name)
);

INSERT INTO heartrate_v3(pet_chip_id, time, heart_rate, pet_name) 
VALUES (123e4567-e89b-12d3-a456-426655440b23, '2019-03-04 07:01:10', 90, 'Duke'); 
```

this query wont work, cuz we need both the partition keys

```sql
SELECT * from heartrate_v3 
WHERE pet_chip_id = 123e4567-e89b-12d3-a456-426655440b23;
```

\----------------------------

it is possible to define (do this):

```sql
CREATE TABLE heartrate_v4 (
   pet_chip_id uuid,
   time timestamp,
   heart_rate int,
   pet_name text,
   PRIMARY KEY (pet_chip_id, pet_name, heart_rate)
);
```

* If there is more than one column in the Clustering Key (pet\_name and heart\_rate in the example above), the order of these columns defines the clustering order. For a given partition, all the rows are physically ordered inside ScyllaDB by the clustering order. This order determines what select query you can efficiently run on this partition.
* In this example, the ordering is first by pet\_name and then by heart\_rate.
* In addition to the Partition Key columns, a query may include the Clustering Key. If it does include the Clustering Key columns they must be used in the same order as they were defined.

It **fails**, as `pet_name` comes before `heart_rate` sin the clustering key.

```sql
SELECT * from heartrate_v4 
WHERE pet_chip_id = 123e4567-e89b-12d3-a456-426655440b23 
AND heart_rate = 100;
```

By default, sorting is based on the natural (ASC) order of the clustering columns.

In order to get query in DESC order, we would need to define the table in the same way

```sql
CREATE TABLE heartrate_v5 (
   pet_chip_id uuid,
   time timestamp,
   heart_rate int,
   PRIMARY KEY (pet_chip_id, time)
) WITH CLUSTERING ORDER BY (time DESC);
```

### Creating Partitions

We don't need partitions size to grow too big, so we can break them by using composite partition key. For example&#x20;

```
CREATE TABLE heartrate_v2 (
    pet_chip_id  uuid,
    date text,
    time timestamp,
    heart_rate int,
    PRIMARY KEY ((pet_chip_id,date), time));
```

In this case, we would be able to query by pet\_chip, for a given day, without having large partitions. The partition size will be limited by the day. Every day, a new partition will be created for each pet.
