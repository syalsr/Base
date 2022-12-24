# Представления VIEW
С помощью VIEW мы можем создавать таблицу на основе колонок других таблиц, данные являются ссылками на оригинальную таблицу.

Представление `customers_v` будет содержать столбцы `id, name from customers`
```sql
CREATE VIEW customers_v id, name AS SELECT id, name FROM customers
SELECT * FROM customers_v
```

```sql
CREATE VIEW products_v
AS SELECT p.id AS id,
			p.name AS product_name,
			t.type_name AS product_type,
			p.price AS product_price
FROM products AS p JOIN product_types AS t
ON p.type_id = t.id

SELECT * FROM products_v

DROP products_v
```

# MATERIALIZED VIEW
Данные копируются.