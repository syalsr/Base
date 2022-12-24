# Создание таблиц Create
```sql
create table publisher
(
	publisher_id integer primary key,
	org_name varchar(128) not null,
	address text not null
);

create table book
(
	book_id serial primary key,
	title text not null,
	isbn varchar(32) not null
)
```

Указывая serial мы хотим, чтобы тип был int и id само увеличивалось при добавление элементов в таблицу book, иначе придется вручную добавлять еще и id, а для этого надо смотреть какой последний id, чтобы не было ошибок.

# Ограничения
1. Primary key - уникальный идентификатор который не может быть NULL
2. UNIQUE - уникальное значение
3. NOT NULL - значение не может быть NULL
4. CHECK - условие для вставки данных 
	1. `price INT CHECK (price >=0)` - не сможем вставить отрицательные данные
	2. `price INT CONSTRAINT positive_price CHECK (price >= 0)`
	3. `CONSTRAIN positive_price CHECK (price >= 0)`
5. REFERENCES - указание на то что эти данные ссылаются на данные другой таблице, чтобы избежать ситуации, когда мы удаляем данные на которые ссылаются какие-то данные. `type_id INT REFERENCES product_types(id)`. Если на поле id таблицы products_types ссылается какое-то поле из другой таблицы, то возникнет ошибка.
	1. `type_id INT REFERENCES product_types(id) ON DELETE RESTRICT` - запрещаем удаление
	2. `type_id INT REFERENCES product_types(id) ON DELETE CASCADE` - удаляем везде, где есть ссылки на id
	3. `type_id INT REFERENCES product_types(id) ON UPDATE RESTRICT` - запрещаем обновление
	4. `type_id INT REFERENCES product_types(id) ON UPDATE CASCADE` - в случае, данные будут обновлены
	5. Можно также указать, что ключ является внешним с помощью [[Связи между таблицами|foreign key]] `FOREIGN KEY(type_id) REFERENCES product_types(id)` 

Ограничения указываются после типа данных

Первичных ключей может быть несколько, для указания нужно писать их в круглых скобках:

```sql
create table publisher
(
	org_name varchar(128) not null,
	address text not null
	PRIMARY KEY(publisher_id customer_id),
);
```