# Window

## Rollup

The ROLLUP option allows you to include extra rows that represent the subtotals, which are commonly referred to as super-aggregate rows, along with the grand total row. 

The ROLLUP assumes a hierarchy among the input columns. 
For example, if the input column is (c1,c2), the hierarchy `c1 > c2`. 

```sql

-- SYNTAX
SELECT 
    c1, c2, aggregate_function(c3)
FROM
    table
GROUP BY ROLLUP (c1, c2);

-- EXAMPLE

SELECT region, round(avg(imports), 2)
FROM trades
GROUP BY rollup (region);

-- OUTPUT

    region      |      round      
-----------------+-----------------
                |  73325290066.17
NORTH AMERICA   |  70993412846.58
SOUTH AMERICA   | 152968951703.49
ASIA            | 115476296703.29
CENTRAL AMERICA |  38853979545.30


SELECT region, country, round(avg(imports), 2)
FROM trades
WHERE country in ('USA', 'Argentina', 'Singapore', 'Brazil')
GROUP BY rollup (region, country)
order by 1;

    region     |  country  |      round       
---------------+-----------+------------------
 ASIA          | Singapore |  209191973057.39
 ASIA          |           |  209191973057.39
 SOUTH AMERICA | Argentina |   40764435340.44
 SOUTH AMERICA | Brazil    |  103647680768.84
 SOUTH AMERICA | USA       | 1590283700017.03
 SOUTH AMERICA |           |  579677530557.70
               |           |  482346579011.01


SELECT region, country, round(avg(imports / 1000000))
FROM trades
WHERE country in ('USA', 'France', 'Germany')
GROUP BY cube (region, country);

    region     | country |  round  
---------------+---------+---------
               |         |  974454
 EUROPE        | France  |  481421
 SOUTH AMERICA | USA     | 1590284
 EUROPE        | Germany |  800653
 SOUTH AMERICA |         | 1590284
 EUROPE        |         |  649743
               | USA     | 1590284
               | Germany |  800653
               | France  |  481421


SELECT region, country, round(avg(imports) / 1000000, 2)
FROM trades
WHERE country in ('USA', 'FRANCE', 'Germany')
GROUP BY
    grouping sets ( (), country, region );

    region     | country |   round    
---------------+---------+------------
               |         | 1195468.31
               | USA     | 1590283.70
               | Germany |  800652.92
 SOUTH AMERICA |         | 1590283.70
 EUROPE        |         |  800652.92



SELECT region, country, round(avg(imports) / 1000000, 2)
FROM trades
WHERE country in ('USA', 'FRANCE', 'Germany')
GROUP BY
    grouping sets ( (), country, region );

    region     | country |   round    
---------------+---------+------------
               |         | 1195468.31
               | USA     | 1590283.70
               | Germany |  800652.92
 SOUTH AMERICA |         | 1590283.70
 EUROPE        |         |  800652.92


SELECT region,
       avg(exports)                                     as avg_all,
       avg(exports) filter ( WHERE trades.year < 1995 ) as avg_old,
       avg(exports) filter ( WHERE trades.year >= 1995) as avg_latest
FROM trades
GROUP BY
    rollup (region);

     region      |        avg_all        |        avg_old        |      avg_latest       
-----------------+-----------------------+-----------------------+-----------------------
                 |  72443407670.13915858 |  42905138774.45808383 |  74914795899.38702405
 NORTH AMERICA   |  74417101760.04615385 |  62932794640.69230769 |  75693135884.41880342
 SOUTH AMERICA   | 109338636484.50140845 |  49195642528.37777778 | 118069071091.03548387
 ASIA            | 122562815407.87438424 |  59879433497.46250000 | 129413458239.61338798
 CENTRAL AMERICA |  34961597492.66203704 |  12904661532.05555556 |  36966773489.08080808


SELECT AVG(imports), avg(exports)
FROM trades;

         avg          |         avg          
----------------------+----------------------
 73325290066.16504854 | 72443407670.13915858
```

## More Queries

```sql

SELECT country, year, imports, exports, avg(exports) OVER () as avg_exports
FROM trades;

SELECT country, year, imports, exports, avg(exports) OVER (partition by country) as avg_exports
FROM trades;

SELECT country, year, imports, exports, avg(exports) OVER (partition by year < 2000 ) as avg_exports
FROM trades;


SELECT country, year, exports, min(exports) OVER (PARTITION BY country order by year)
FROM trades
WHERE year > 2001
  and country in ('USA', 'France');
```

