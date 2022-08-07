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