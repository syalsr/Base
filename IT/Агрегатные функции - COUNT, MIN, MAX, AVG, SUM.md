# Агрегатные функции - COUNT, MIN, MAX, AVG, SUM
```sql
SELECT MIN("OrderDate")
FROM orders
WHERE "ShipCity" = 'London'
1996-08-26

SELECT MAX("OrderDate")
FROM orders
WHERE "ShipCity" = 'London'
1998-04-29

SELECT AVG("UnitPrice")
FROM products
28.8338960920061

SELECT SUM("UnitPrice")
FROM products
2220.21

SELECT COUNT(DISTINCT "Country") 
FROM employees
2 - работники работют из двух стран
```