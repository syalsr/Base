# Friends
Предоставляет функции или другому классу доступ к закрытым и защищенным членам класса, в котором появилось объявление friend.

```cpp
void printFoo(const Foo&);
class Foo
{
	friend class Bar;//весь класс
	friend void Bar::processFoo(const Foo&);//только один метод
	friend void printFoo(const Foo&);//функция исходя из логики класса не может быть определена внутри класса, но ей нужен доступ к ее членам
	// ...
};
```