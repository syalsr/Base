# WHERE
 Инструкция для установки условия для вывода данных
```sql
SELECT "CompanyName", "ContactName", "Phone", "Country"
FROM customers
WHERE "Country" = 'USA'

SELECT COUNT(*)
FROM products
WHERE "UnitPrice" > 10

SELECT *
FROM "Orders"
WHERE "OrdersDate" BETWEEN '1998-03-30' AND '1999-03-30'
```

## = != <> > >= < <=
```sql
SELECT COUNT(*)
FROM products
WHERE "UnitPrice" > 10
```

## LIKE
Ищем строку по паттерну
```sql
% - заполнитель означающий 0, 1 и более символов 
LIKE 'U%' - строки начинающиеся с U
LIKE '%Z' - строки кончающиеся на Z
_ - ровно 1 любой символ
LIKE '_ELLO'

SELECT "LastName", "FirstName"
FROM employees
WHERE "FirstName" LIKE '%t'
"Leverling"	"Janet"
"Peacock"	"Margaret"
"King"	     "Robert"
```

## BETWEEN 
Позволяет указать границы значений.

```sql
SELECT COUNT(*)
FROM products
WHERE "UnitPrice" BETWEEN 10 and 20(либо можно написать так "UnitPrice" >= 10 AND "UnitPrice" <= 20 )
```

## IN
Позволяет указать находится ли значение в некотором списке
```sql
SELECT *
FROM "Customers"
WHERE "Country" IN ('Mexico', 'Germany', 'USA') (либо можно написать так "Country" = 'Mexico' OR "Country" = 'Germany' OR "Country" = 'USA')
```

## NOT IN
```sql
SELECT *
FROM "Customers"
WHERE "Country" NOT IN ('Mexico', 'Germany', 'USA') (либо можно написать так "Country" != 'Mexico' AND "Country" != 'Germany' AND "Country" != 'USA')
```

## AND OR NOT
```sql
SELECT *
FROM superheroes
WHERE gender = 'Female' OR gender = 'Male' AND NOT character = 'Good'
```