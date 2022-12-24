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
INNER JOIN suppliers ON products."SupplierID" = suppliers."SupplierID"//поссле ON указываем условия, что id должны совпадать
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

select employees."EmployeeID", employeeterritories."TerritoryID" from employees
inner join employeeterritories ON employees."EmployeeID" = employeeterritories."EmployeeID"
where employees."EmployeeID" = 1
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
