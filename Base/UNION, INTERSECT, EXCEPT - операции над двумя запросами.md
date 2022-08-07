# UNION, INTERSECT, EXCEPT - операции над двумя запросами
```sql
SELECT "Country"
FROM customers
UNION - объединение
SELECT "Country"
FROM employees

SELECT "Country"
FROM customers
INTERSECT - пересечение
SELECT "Country"
FROM employees

SELECT "Country"
FROM customers
EXCEPT - исключение
SELECT "Country"
FROM employees
```