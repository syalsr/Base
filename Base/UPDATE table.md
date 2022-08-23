# Изменение таблицы
## insert into
Заполнение данных
```sql
insert into book
values
(1, 'The diary of a young girl', '0199535566'),
(2, 'Pride and prejudice', '9780307594006'),
(3, 'War abd peace', '1788886526'),
(4, 'The book of gutsy woment', '1501178415');

insert into superheroes(name, align, eye, hair)
values ('Spider-Man', 'Good', 'Hazel', 'Brown')
```

Во втором случае, мы вставляем данные в конкретные поля, рекомендуемый синтаксис, поскольку таблица может измениться и в первом случае данные вставятся не туда куда нужно.

## update table
Обновление значений
```sql
update book
set fk_publisher_id = 3 where book_id = 4
```
