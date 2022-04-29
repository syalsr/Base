# Templates
# Зачем нужны шаблоны
Если у нас есть несколько функций с одинаковым поведением, но разными типами параметров, либо несколько классов с одинаковым набором методов и данных, но с разными типами этих методов и данных, то можно сделать шаблон функции/шаблон класса, чтобы уменьшить повторяющийся код

1. [[Шаблоны функций]]
	1. [[Разрешения перегрузки шаблонов и обычных функций]]
	2. [[Указание возвращаемого типа у шаблонов функций]]
2. [[Шаблоны классов]]
	1. [[Специализация шаблонов классов]]
	2. [[Частичная специализация шаблонов классов]]
3. [[Инстанцирование]]
4. [[Явное инстанцирование]]
5. [[Двухэтапная трансляция]]
6. [[Вариативные шаблоны variadic templates]]
7. [[Вывод типов Type deduction]]
8. [[Категории типов в C++]]
9. [[Свойства типов Type traits]]
10. [[Шаблоны глобальных переменных]]
11. [[Конфликты имен]]
12. [[Параметры шаблонов не являющиеся типами]]
13. [[Универсальные ссылки perfect forwarding]]
14. [[SFINAE]]
15. [[Шаблоны специальных методов]]
16. [[void_t]]
17. [[enable_if]]
18. [[is_reference remove_reference]]



# Инициализация объектов по умолчанию
```cpp
//befor C++11
template<typename T>
class X
{
	T x();
	X() : x()
	{}
	void test(T x = T());
}

//since C++11
template<typename T>
class X
{
	T x{};
	X() : x{}
	{}
	void test(T x = T{});
}
```

# Обобщенные лямбда выражения и шаблоны членов
Такие сокращенные лямбда выражения

```cpp
[] (auto x, auto y) {
	return x + y;
}
```

Трансформируются в это

```cpp
class SomeCompilerSpecificName {
public:
	SomeCompilerSpecificName(); // constructor only callable by compiler
	
	template<typename T1, typename T2>
	auto operator() (T1 x, T2 y) const {
		return x + y;
	}
};
```

# declval
Это функция стандартной библиотеки которая имеет только объявления делает из нас rvalue ref

```cpp
template <typename T> struct Tricky {  
    Tricky() = delete;  
    const volatile T foo ();  
};  
int main() {  
    decltype(declval<Tricky<int>>().foo()) t; // int  
}
```

delctype только оценивает тип, поскольку у declval нет тела, все хорошо.

## Конструирование вектора из итераторов
Проблема в том, что передав 2 числа, вызовется конструктор для итераторов, хотя не хотелось бы. 

```cpp
MyVector(size_t nelts, T value);  
template <typename Iter,  
        typename = void_t<decltype(*Iter{}),  
		                  decltype(++Iter{})>//сомнитьтельно, префиксу нужно lvalue  
         >  
MyVector(Iter fst, Iter lst);
```

Вот как это решить:

```cpp
MyVector(size_t nelts, T value);  
template <typename Iter,  
        typename = void_t<decltype(*std::declval<Iter&>()), //вернет l ref 
		                  decltype(++std::declval<Iter&>())>//вернет l ref
        > 				 
MyVector(Iter fst, Iter lst);  
....  
MyVector v1(10, 3); // 1, поскольку 2 провалилось  
MyVector v2(v1.begin(), v1.end()); // 2
```

Эти 2 decltype можно представить как интерфейс void_t, то есть то что он должен уметь, чтобы успешно выполнилась работа конструктора - этот интерфейс называется [[Consepts|концептом]]



