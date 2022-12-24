# HAVING
Аналог where, но можно использовать после группировки
```sql
SELECT "CategoryID", SUM("UnitPrice" * "UnitsInStock")
FROM products
WHERE "Discontinued" != 1
GROUP BY "CategoryID"
8	13010.3499145508
7	2363.75
1	11365.25
5	5230.5
4	11271.1999893188
2	11926.0499992371
6	2916.44999599457
3	10392.2000722885

SELECT "CategoryID", SUM("UnitPrice" * "UnitsInStock")
FROM products
WHERE "Discontinued" != 1
GROUP BY "CategoryID"
HAVING SUM("UnitPrice" * "UnitsInStock") > 5000
ORDER BY SUM("UnitPrice" * "UnitsInStock")
5	5230.5
3	10392.2000722885
4	11271.1999893188
1	11365.25
2	11926.0499992371
8	13010.3499145508
```