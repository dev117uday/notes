# Stored Procedures

- They are compiled objects

- The procedure allows SELECT as well as DML(INSERT/UPDATE/DELETE) statement in it whereas Function allows only SELECT statement in it.

- Procedures cannot be utilized in a SELECT statement whereas Function can be embedded in a SELECT statement.

- Stored Procedures cannot be used in the SQL statements anywhere in the WHERE/HAVING/SELECT section whereas Function can be.

- Functions that return tables can be treated as another rowset. This can be used in JOINs with other tables.

- Inline Function can be though of as views that take parameters and can be used in JOINs and other Rowset operations.

- An exception can be handled by try-catch block in a Procedure whereas try-catch block cannot be used in a Function.

- We can use Transactions in Procedure whereas we can't use Transactions in Function.

#### Sample Data
```sql
CREATE TABLE t_accounts (
    recid SERIAL PRIMARY KEY,
    name VARCHAR NOT NULL,
    balance dec(15,2) NOT NULL
);

drop table t_accounts;

INSERT INTO t_accounts (name,balance) values ('Adam',100),('Linda',100);

select * from t_accounts;

```

## Creating Procedure

```sql

CREATE OR REPLACE PROCEDURE pr_money_transfer 
    (sender int, receiver int, amount dec) 
AS
    $$

        BEGIN

            UPDATE t_accounts
            SET balance = balance - amount
            WHERE recid = sender;

            UPDATE t_accounts
            SET balance = balance + amount
            WHERE recid = receiver;

            COMMIT;

        END;
    $$
LANGUAGE PLPGSQL;

CALL pr_money_transfer(1,2,30);

select * from t_accounts;
```

## Another Example

```sql
CREATE OR REPLACE PROCEDURE pr_orders_count(INOUT total_count INTEGER DEFAULT 0 ) AS
$$
    BEGIN

        SELECT COUNT(*) INTO total_count FROM orders;

    END;
$$ LANGUAGE PLPGSQL;

CALL pr_orders_count();
```

