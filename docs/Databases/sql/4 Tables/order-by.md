---
description: a clause
---

# ORDER BY and DISTINCT

## Order BY

```sql
SELECT
    company_name,
    contact_name
FROM
    customers
ORDER BY
    company_name DESC,
    contact_name
LIMIT 10;

           company_name            |      contact_name       
-----------------------------------+-------------------------
 Wolski  Zajazd                    | Zbyszek Piestrzeniewicz
 Wilman Kala                       | Matti Karttunen
 White Clover Markets              | Karl Jablonski
 Wellington Importadora            | Paula Parente
 Wartian Herkku                    | Pirkko Koskitalo
 Vins et alcools Chevalier         | Paul Henriot
 Victuailles en stock              | Mary Saveley
 Vaffeljernet                      | Palle Ibsen
 Trails Head Gourmet Provisioners  | Helvetius Nagy
 Tradição Hipermercados            | Anabela Domingues


SELECT 
    orderNumber, 
    orderlinenumber, 
    quantityOrdered * priceEach as final_price
FROM
    orderdetails
ORDER BY 
   final_price DESC LIMIT 10;
   
-- orders
    order_id | product_id |    total_price     
----------+------------+--------------------
    10981 |         38 |              15810
    10865 |         38 |              15810
    10353 |         38 |              10540
    10417 |         38 |              10540
    10889 |         38 |              10540
    10424 |         38 |              10329
    10897 |         29 |               9903
    10372 |         38 |               8432
    10816 |         38 |               7905
    10540 |         38 |               7905
(10 rows)


-- using alias in order by
SELECT
    first_name,
    last_name as surname
FROM
    actors
ORDER BY
    surname DESC ;

 first_name | surname 
------------+---------
 Ziyi       | Zhang
 Billy      | Zane
 Sean       | Young
 Jin-seo    | Yoon
 Ji-tae     | Yoo

-- NULLS FIRST AND LAST
select *
from actors
order by gender NULLS LAST
LIMIT 5;

 first_name | last_name | gender |    date_of_birth    
------------+-----------+--------+---------------------
 Malin      | Akerman   | F      | 1978-05-12 00:00:00
 Julie      | Andrews   | F      | 1935-10-01 00:00:00
 Ivana      | Baquero   | F      | 1994-06-11 00:00:00
 Lorraine   | Bracco    | F      | 1954-10-02 00:00:00
 Alice      | Braga     | F      | 1983-04-15 00:00:00

```

### **ORDER BY on multiple columns**

The following statement selects the first name and last name from the customer table and sorts the rows by the first name in ascending order and last name in descending order:

```sql
SELECT
    first_name,
    last_name
FROM
    customer
ORDER BY
    first_name ASC,
    last_name DESC;
```

| First Name | Last Name |
| :--- | :--- |
| Kelly | Torres |
| Kelly | Knott |

In this example, the ORDER BY clause sorts rows by values in the first name column first. And then it sorts the sorted rows by values in the last name column.

As you can see clearly from the output, two customers with the same first name Kelly have the last name sorted in descending order.

## DISTINCT

```sql
SELECT DISTINCT region
FROM customers
WHERE country = 'USA'
LIMIT 10;
    
 region 
--------
 NM
 CA
 AK
 WY
 OR
 MT
 ID
 WA
(8 rows)

```

### Distinct COUNT

```sql
SELECT COUNT(DISTINCT region)
FROM customers
WHERE country = 'USA';

 count 
-------
     8
```



