# C++
## Метод
Статические методы, данные распространяются не на один объект, а на все созданные.

```cpp
class A
{
public:
	A() {}
	static void b() {
		x = 34;//error
	}
private:
	int x = 5;
};

int main()
{
	A::b();//Раз он не применяется к конкретному объекту, то у него нет неявного параметра this, поэтому нам не нужен какой-то объект для его вызова, но мы можем его вызывать с помощью объекта
}
```

Не могут быть константными, т.к. не изменяют внутреннее состояние объекта

Если метод не меняет состояние объекта, то можно сделать его static, к примеру, у класса Car, может быть 2 static метода, один переводит км в мили, другой мили в километры и принимают значение для перевода, они не влияют на состояние объекта, но являются частью функциональности машины, поскольку в машину могут захотеть добавить такую возможность.

## Данные
Допустим вы хотите знать количество объектов класса, для этого можно завести статическую переменную которая будет принадлежать не не каждому объекту а классу, каждый объект будет иметь к ней доступ

```cpp
class unique_ptr1
{
public:
	unique_ptr1() : id(counter++) {
	}
static size_t counter;
const size_t id;
};

size_t unique_ptr1::counter;//default initialize 0, but can
size_t unique_ptr1::counter { 0 };

int main()
{
	unique_ptr1 textHolder;
	unique_ptr1 textHolder1;
	unique_ptr1 textHolder2;
}
```

## const static
```cpp
class Spreadsheet
{
public:
	Spreadsheet(size_t width = MaxWidth, size_t height = MaxHeight)
		: m_width { min(width, MaxWidth) }
		, m_height { min(height, MaxHeight) }
	{
	}
	static const size_t MaxHeight { 100 };//Чтобы всякий раз не выделять память
	static const size_t MaxWidth { 100 };//делаем максимальные значение длины и высоты static const
};
```

## Переменная static в функциях
Сохраняет свое значение после окончания работы функции. Т.е. это глобальная переменная, но доступна внутри функции и только.

Для чего это нужно, допустим мы хотим знать выполнялось ли инициализирование переменной.
```cpp
void performTask()
{
	static bool initialized{ false };
	if (!initialized) {
		cout << "initializing" << endl;
		// Perform initialization.
		initialized = true;
	}
	// Perform the desired task.
}
```

Но лучше такого избегать, если это класс - то использовать конструктор по умолчанию, если базовый тип, сразу задавать значение.

# Java
Статические данные, методы распространяются на весь класс, а не на конкретный объект, они не могут быть полиморфмными.

```java
Class Base{
	public static int val;
	public static final newv = 3124;
	public static void print()
	{
		System.out.println(val + newv);
	}
}

Base.val = 2;
Base.print();
```

К слову, `System` - класс у которого есть статическое поле `out` у которого мы вызываем метод `print`.

* [[Блоки инициализации Java]]