# LIMIT
С помощью этого ключевого слова можем ограничить количество выводимых данных
```sql
SELECT "LastName", "FirstName"
FROM employees
"Davolio"	"Nancy"
"Fuller"	"Andrew"
"Leverling"	"Janet"
"Peacock"	"Margaret"
"Buchanan"	"Steven"
"Suyama"	"Michael"
"King"	"Robert"
"Callahan"	"Laura"
"Dodsworth"	"Anne"

SELECT "LastName", "FirstName"
FROM employees
LIMIT 1
"Davolio"	"Nancy"
```