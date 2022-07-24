# SQL - язык для взаимодействия с реляционными БД
`SELECT *  FROM Name_table `- вся таблица
`SELECT Id  FROM Name_table`  - колонка Id
`SELECT Id, Title  FROM Name_table`  - колонки Id, Title
```sql
SELECT Title  FROM Movies
WHERE Id = 6
Выведет название 6 фильма
```

```sql
SELECT column, another_column, … FROM mytable 
WHERE _condition_ AND/OR _another_condition_ AND/OR …;
```

| Operator            | Condition                                            | SQL Example                   |
| ------------------- | ---------------------------------------------------- | ----------------------------- |
| =, !=, < <=, >, >=  | Standard numerical operators                         | col_name != 4                 |
| BETWEEN … AND …     | Number is within range of two values (inclusive)     | col_name BETWEEN 1.5 AND 10.5 |
| NOT BETWEEN … AND … | Number is not within range of two values (inclusive) | col_name NOT BETWEEN 1 AND 10 |
| IN (…)              | Number exists in a list                              | col_name IN (2, 4, 6)         |
| NOT IN (…)          | Number does not exist in a list                      | col_name NOT IN (1, 3, 5)     |

| Operator   | Condition                                                                                             | Example                                                              |
| ---------- | ----------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| =          | Case sensitive exact string comparison (_notice the single equals_)                                   | col_name = "abc"                                                     |
| != or <>   | Case sensitive exact string inequality comparison                                                     | col_name != "abcd"                                                   |
| LIKE       | Case insensitive exact string comparison                                                              | col_name LIKE "ABC"                                                  |
| NOT LIKE   | Case insensitive exact string inequality comparison                                                   | col_name NOT LIKE "ABCD"                                             |
| %          | Used anywhere in a string to match a sequence of zero or more characters (only with LIKE or NOT LIKE) | col_name LIKE "%AT%"   (matches "AT", "ATTIC", "CAT" or even "BATS") |
| `_`        | Used anywhere in a string to match a single character (only with LIKE or NOT LIKE)                    | col_name LIKE "AN_"  (matches "AND", but not "AN")                   |
| IN (…)     | String exists in a list                                                                               | col_name IN ("A", "B", "C")                                          |
| NOT IN (…) | String does not exist in a list                                                                       | col_name NOT IN ("D", "E", "F")                                      |


Создание 2 таблиц
```sql
create table publisher
(
	publisher_id integer primary key,
	org_name varchar(128) not null,
	address text not null
);

create table book
(
	book_id integer primary key,
	title text not null,
	isbn varchar(32) not null
)
```

Заполнение данных
```sql
insert into book
values
(1, 'The diary of a young girl', '0199535566'),
(2, 'Pride and prejudice', '9780307594006'),
(3, 'War abd peace', '1788886526'),
(4, 'The book of gutsy woment', '1501178415');

insert into publisher
values
(1, 'Everyman''s Library', 'NY'),
(2, 'Oxford University Press', 'NY'),
(3, 'Grand Central Publishing', 'Washington'),
(4, 'Simon & Schuster', 'Chicago');
```

Добавление новой колонки

```sql
alter table book 
add column fk_publisher_id integer
```

Связывание этой колонки с колонкой в другой таблице
```sql
alter table book 
add constraint fk_publisher
foreign key(fk_publisher_id) references publisher(publisher_id)
```

Обновление значений
```sql
update book
set fk_publisher_id = 3 where book_id = 4
```

# Операторы сравнения
```sql
a = b
a > b
a >= b
a < b
a <= b
a <> b or a !=b
```

# Логические операторы
```sql
AND
OR

BETWEEN - включены обе границы
SELECT COUNT(*)
FROM products
WHERE "UnitPrice" BETWEEN 10 and 20(либо можно написать так "UnitPrice" >= 10 AND "UnitPrice" <= 20 )

IN
SELECT *
FROM "Customers"
WHERE "Country" IN ('Mexico', 'Germany', 'USA') (либо можно написать так "Country" = 'Mexico' OR "Country" = 'Germany' OR "Country" = 'USA')

NOT IN
SELECT *
FROM "Customers"
WHERE "Country" NOT IN ('Mexico', 'Germany', 'USA') (либо можно написать так "Country" != 'Mexico' AND "Country" != 'Germany' AND "Country" != 'USA')
```

# DISTINCT - убирает дубликаты
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

# COUNT - подсчет строк
```sql
SELECT COUNT(DISTINCT "Country") 
FROM employees
2 - работники работют из двух стран
```

# WHERE - инструкция для установки условия
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

# ORDER BY - сортировка, ASC - по возрастанию, DESC - по убыванию
```sql
SELECT DISTINCT "Country"
FROM customers
ORDER BY "Country" ASC

SELECT DISTINCT "Country"
FROM customers
ORDER BY "Country" DESC

SELECT DISTINCT "Country", "City"
FROM customers
ORDER BY "Country", "City"
"Argentina"	"Buenos Aires"
"Austria"	"Graz"
"Austria"	"Salzburg"
"Belgium"	"Bruxelles"
"Belgium"	"Charleroi"
"Brazil"	"Campinas"
"Brazil"	"Resende"
"Brazil"	"Rio de Janeiro"
"Brazil"	"Sao Paulo"
"Canada"	"Montréal"
"Canada"	"Tsawassen"
"Canada"	"Vancouver"
Внутри каждой страны, города между собой сортируются

SELECT DISTINCT "Country", "City"
FROM customers
ORDER BY "Country" DESC, "City" ASC
"Venezuela"	"Barquisimeto"
"Venezuela"	"Caracas"
"Venezuela"	"I. de Margarita"
"Venezuela"	"San Cristóbal"
"USA"	"Albuquerque"
"USA"	"Anchorage"
"USA"	"Boise"
"USA"	"Butte"
"USA"	"Elgin"
"USA"	"Eugene"
"USA"	"Kirkland"
"USA"	"Lander"
"USA"	"Portland"
"USA"	"San Francisco"
"USA"	"Seattle"
```

# MIN, MAX, AVG, SUM
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
```

# LIKE - ищем строку по паттерну
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

# LIMIT - ограничение по выводу
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

# CHECK on NULL - проверка на null
```sql
SELECT "ShipCity", "ShipRegion", "ShipCountry"
FROM orders
WHERE "ShipRegion" IS NULL
LIMIT 5
"Reims"		null    "France"
"Münster"	null	"Germany"
"Lyon"		null    "France"
"Charleroi"	null	"Belgium"
"Bern"		null    "Switzerland"
```

# GROUP BY - группировка по
```sql
SELECT "ShipCountry", COUNT(*)
FROM orders
WHERE "Freight" > 50
GROUP BY "ShipCountry" группируем по каждой стране
ORDER BY COUNT(*) DESC
"USA"	    61
"Germany"	58
"Austria"	33
"Brazil"	32
"France"	27
"Venezuela"	20
"Sweden"	20
"UK"	    17
"Canada"	13
"Ireland"	12
"Mexico"	9
"Switzerland"	9
"Denmark"	9
"Belgium"	8
"Spain"	    7
"Finland"	6
"Italy"	    6
"Portugal"	6
"Norway"	3
"Argentina"	3
"Poland"	1

SELECT "CategoryID", SUM("UnitsInStock")
FROM products
GROUP BY "CategoryID"
ORDER BY SUM("UnitsInStock") DESC
LIMIT 5
8	701
1	559
2	507
4	393
3	386
```

# HAVING - аналог where, но можно использовать после группировки
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

# JOIN
Левая таблица - Publisher, Правая таблица - Book
 | id  | name             | address    | Пустое место | id  | title         | ISBN | fk_publisher_id |
 | --- | ---------------- | ---------- | ------------ | --- | ------------- | ---- | --------------- |
 | 1   | Everymans        | NY         |              | 1   | The Diary     | 111  | 1               |
 | 2   | Oxford           | NY         |              | 2   | Pride         | 222  | 1               |
 | 3   | Grand central    | Washington |              | 3   | To Kill       | 333  | 2               |
 | 4   | Simon & Schuster | Chicago    |              | 4   | The Book      | 444  | 2               |
 | 5   | West coast       | Chicago    |              | 5   | War and Peace | 555  | 2               |

 ## INNER JOIN
 В результирующую таблицу попадают только те строки в которых есть совпадение по ключу

![[innerjoin.excalidraw]]

| id  | name      | address | title         | ISBN |
| --- | --------- | ------- | ------------- | ---- |
| 1   | Everymans | NY      | The Diary     | 111  |
| 1   | Everymans | NY      | Pride         | 222  |
| 2   | Oxford    | NY      | To Kill       | 333  |
| 2   | Oxford    | NY      | The Book      | 444  |
| 2   | Oxford    | NY      | War and Peace | 555  |

```sql
Выводим наимеование продукта, компании и количество
SELECT "ProductName", "CompanyName", "UnitsInStock"
FROM products
INNER JOIN suppliers ON products."SupplierID" = suppliers."SupplierID"
ORDER BY "UnitsInStock" DESC
LIMIT 10
"Rhönbräu Klosterbier"	"Plutzer Lebensmittelgroßmärkte AG"	125
"Boston Crab Meat"	"New England Seafood Cannery"	123
"Grandma's Boysenberry Spread"	"Grandma Kelly's Homestead"	120
"Pâté chinois"	"Ma Maison"	115
"Sirop d'érable"	"Forêts d'érables"	113
"Geitost"	"Norske Meierier"	112
"Inlagd Sill"	"Svensk Sjöföda AB"	112
"Sasquatch Ale"	"Bigfoot Breweries"	111
"Gustaf's Knäckebröd"	"PB Knäckebröd AB"	104
"Röd Kaviar"	"Svensk Sjöföda AB"	101
```

## LEFT OUTER JOIN
При таком соединении в результирующую таблицу попадают все строки из левой таблицы, даже если нет соответствия в правой, но значения `null`

![[leftjoin.excalidraw]]

| id  | name             | address    | title         | ISBN |
| --- | ---------------- | ---------- | ------------- | ---- |
| 1   | Everymans        | NY         | The Diary     | 111  |
| 1   | Everymans        | NY         | Pride         | 222  |
| 2   | Oxford           | NY         | To Kill       | 333  |
| 2   | Oxford           | NY         | The Book      | 444  |
| 2   | Oxford           | NY         | War and Peace | 555  |
| 3   | Grand central    | Washington | NULL          | NULL |
| 4   | Simon & Schuster | Chicago    | NULL          | NULL |
| 5   | West coast       | Chicago    | NULL          | NULL |

## RIGHT OUTER JOIN
При RIGHT OUTER JOIN все тоже самое, только берутся все значения из правой таблицы. Обычно всегда используют LEFT OUTER JOIN, применяя ее к левой или правой таблицы.

![[rightjoin.excalidraw]]

| id  | name      | address | title         | ISBN |
| --- | --------- | ------- | ------------- | ---- |
| 1   | Everymans | NY      | The Diary     | 111  |
| 1   | Everymans | NY      | Pride         | 222  |
| 2   | Oxford    | NY      | To Kill       | 333  |
| 2   | Oxford    | NY      | The Book      | 444  |
| 2   | Oxford    | NY      | War and Peace | 555  |

## FULL OTER JOIN
Это LEFT + RIGHT OUTER JOIN

![[fulljoin.excalidraw]]

| id  | name             | address    | title         | ISBN |
| --- | ---------------- | ---------- | ------------- | ---- |
| 1   | Everymans        | NY         | The Diary     | 111  |
| 1   | Everymans        | NY         | Pride         | 222  |
| 2   | Oxford           | NY         | To Kill       | 333  |
| 2   | Oxford           | NY         | The Book      | 444  |
| 2   | Oxford           | NY         | War and Peace | 555  |
| 3   | Grand central    | Washington | NULL          | NULL |
| 4   | Simon & Schuster | Chicago    | NULL          | NULL |
| 5   | West coast       | Chicago    | NULL          | NULL |



