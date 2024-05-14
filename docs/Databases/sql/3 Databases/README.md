# Database

## Creating a database

![image](./assets/create-database.gif)

## Dropping Database

![image](./assets/dropping-database.gif)

## Commands

```sql
create database testdb;

drop database if exists testdb;
```

### Connect to DB

```sql
-- using psql
\c test
```

### See list of tables

```sql
-- using psql
\d                       #to see tables
\d <name of table>       #to see details about table
```

