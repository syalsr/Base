# static_cast
```cpp
float myFloat { 3.14f };
int i1 { (int)myFloat };
int i2 { int(myFloat) }; 
int i3 { static_cast<int>(myFloat) }; // most recommend
```

Для кастов полиморфных объектов не стоит использовать static_cast т.к. он не проверяет является ли один тип потомком другого $=>$ он может приводить любые типы, что может привести к ошибке. Лучше использовать [[casting C++#dynamic_cast|dynamic_cast]]

# const_cast
С помощью данного приведения мы можем сделать переменную const или убрать ее константность.


# reinterpret_cast
Мы можем его использовать для приведений который технически не разрешены. Приведение ссылке на один тип, к ссылке на другой тип, даже если они не связаны. Также можно привести один указатель к другому, если они не связаны наследованием. Обычно используется для приведения указателя к [[Модель памяти#void|void*]] .

```cpp
class X {};
class Y {};
int main()
{
	X x;
	Y y;
	X* xp{ &x };
	Y* yp{ &y };
	// Need reinterpret_cast for pointer conversion from unrelated classes
	// static_cast doesn't work.
	xp = reinterpret_cast<X*>(yp);
	// No cast required for conversion from pointer to void*
	void* p{ xp };
	// Need reinterpret_cast for pointer conversion from void*
	xp = reinterpret_cast<X*>(p);
	// Need reinterpret_cast for reference conversion from unrelated classes
	// static_cast doesn't work.
	X& xr{ x };
	Y& yr{ reinterpret_cast<Y&>(x) };
}
```

reinterpret_cast накладывает некоторые ограничения на приведение типов

# dynamic_cast
**downcast** - приведения указателя/ссылки базового класса к указателю/ссылки производного класса
**upcast** - приведения указателя, ссылки производного класса к указателю/ссылки базового класса

Предоставляет проверку иерархии типов в ран тайме, он проверяет информацию о типе базового объекта во время выполнения, если приведение не имеет смысла, возвращает nullptr - если указатель, std::bad_cast - если ссылка. 

```cpp
class Base
{
public:
	virtual ~Base() = default;
};
class Derived : public Base
{
public:
	virtual ~Derived() = default;
};
int main()
{
	Base* b;
	Derived* d{ new Derived{} };
	b = d;
	d = dynamic_cast<Derived*>(b);

	Base base;
	Derived derived;
	Base& br{ base };
	try {
		Derived& dr{ dynamic_cast<Derived&>(br) };
	}
	catch (const bad_cast&) {
		cout << "Bad cast!" << endl;
	}
}
```

Он берет всю информацию из виртуальной таблицы $=>$ если мы применяем dynamic_cast к классу который не имеет виртуальных методов, будет ошибка

```cpp
void lessPresumptuous(Base* base)
{
	Derived* myDerived{ dynamic_cast<Derived*>(base) };
	if (myDerived != nullptr) {
		
	}
}
```

Частое использование даункастов - признак плохого дизайна