# struct
Структуры отличаются от класса только тем, что их модификатор доступа по умолчанию public. Также и с наследованием, если его явно не указывать, у класса оно приватное, у структуры публичное по умолчанию. 

Структуры обычно используются для хранения набора данных, допустим координаты точки(x,y). В то время как класс используется еще и для работы с данными путем использования методов.

```cpp
struct inflatable //объявление структуры. inflatable - идентификатор или дескриптор
{
	char name[20];
	float volume;
	double price;
};
inflatable goose; // переменная типа inflatable
goose.price = 5.0;
inflatable guest {"Gloria", 1.99, 29.99};
inflatable guest1 {}; //volume = price = 0; байты name = 0
```

Можно комбинировать опеделение формы структуры с созданием структурных переменных.

```cpp
struct perks
{
	int key_number;
	char car[12];
} mr_smith, ms_jones; // two perks variables

struct new_perks
{
	int key_number;
	char car[12];
} mr_giltz =
{
	7,
	"Packed"
};
```

Можно создать одну структурную переменную, то есть мы не сможем создавать другие переменные этого типа

```cpp
struct // no tag
{
	int x; // 2 members
	int y;
} position; // a structure variable
position.x = 2;
```

Можно указывать размер члена структуры(в битах). Тип поля должен быть целочисленным или перечислимым.

```cpp
struct torgle_register
{
	unsigned int SN : 4; // 4 бита для значения SN
	unsigned int 	: 4; // 4 бита не используются
	bool goodIn		: 1; // Допустимый ввод(1 бит)
	bool goodTorgle	: 1; // Признак успешности
};
torgle_register tr = {14, true, false};
```

# Объединения(union)
Объединение - это формат данных, который может хранить в пределах одной области памяти разные типы данных, но в каждый момент времени только один из них.

```cpp
union one
{
	int 	int_val;
	long   	long_val;
	double 	double_val;
};
```

Переменную one можно использовать для хранения int, long или double, если только делать это не одновременно

```cpp
one pail;
pail.int_val 	= 15; // сохраняем int
pail.double_val = 15.0; //удаляем int, сохраняем double
```

Зачем использовать?

* экономия памяти
* элемент данных может использовать два или более форматов, но никогда - одновременно

Например, предположи м, что вы ведете реестр каких-то предметов, из которых одни имеют целочисленный идентификато р, а другие - строковый.

```cpp
struct widget
{
	char brand[20];
	int type;
	union id // формат зависит от типа предмета
	{
		long id_num; // предмет первого типа
		char id_char[20]; // другие предметы
	} id_val;
};
...
widget prize;
...
if (prize.type == 1)
	cin >> prize.id_val.id_num;
else
	cin >> prize.id_val.id_char;
```

Анонимное объединение не имеет имени; в сущности , его члены становятся переменными, расположенными по одному и тому же адресу в памяти . Разумеется , только одна из них может быть текущей в единицу времени:

```cpp
struct widget
{
	char brand[20];
	int type;
	union // anonymous union
	{
		long id_num; // type 1 widgets
		char id_char[20]; // other widgets
	};
};
...
widget prize;
...
if (prize.type == 1)
	cin >> prize.id_num;
else
	cin >> prize.id_char;
```

#do/review 