Вывод типа позволяют компилятору самому определять тип выражения, функции, метода, объекта.

# auto
Имеет несколько вариантов использования
1. Выводит возвращаемый тип функции
2. Используется для [[structured bindings]]
3. Выводит тип выражения
4. Выводит тип нешаблонных параметров
5. Синтаксис сокращенного шаблона функции
6. decltype(auto)
7. Альтернативный синтаксис функции
8. Лямбда выражения

auto отбрасывает const и ref, но не pointer
```cpp
const int& Foo(int& a) { return a; }
int a = 5;
	  auto  x      { 123    };// x is of type int.
      auto  result { Foo(a) };//int result
const auto& result { Foo(a) };//const int& result

int i { 123 };
auto p { &i };//int*
auto* p { &i };//int*

const auto p { &i };//int* const       unexp
auto const p { &i };//int* const       unexp
const auto* p { &i };//const int*      exp
auto* const p { &i };//int* const      exp
```


# decltype
Исходя из переданного выражения выводит тип, не удаляет квалификатор const и ссылку

```cpp
int x { 123 };
decltype(x) y { 456 };
```

# decltype(auto)
Т.к. auto отбрасывает const и ref, используя decltype можем этого избежать

```cpp
int& f(int& aa)
{
	return a;
}


int main()
{
	int a = 5;
	auto			test = f(a);//int
	decltype(auto)	test = f(a);//int&
}
```