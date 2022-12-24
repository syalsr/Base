# DISTINCT
С помощью этого ключевого слова можем вывести уникальные значения.
```sql
SELECT "Country", "City" 
FROM employees
"USA"	"Seattle"
"USA"	"Tacoma"
"USA"	"Kirkland"
"USA"	"Redmond"
"UK"	"London"
"UK"	"London"
"UK"	"London"
"USA"	"Seattle"
"UK"	"London"

SELECT DISTINCT "Country" 
FROM employees
"UK"
"USA"

SELECT DISTINCT "Country", "City" 
FROM employees
"USA"	"Tacoma"
"USA"	"Seattle"
"USA"	"Kirkland"
"USA"	"Redmond"
"UK"	"London"
```