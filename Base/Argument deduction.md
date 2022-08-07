# Вывод аргументов шаблона класса
До C++17 нужны было указывать все типы шаблона, кроме типов по умолчанию.
```cpp
vector<int> v{1,2,3};//before C++17
vector v{1,2,3};//since C++17
```

## Вывод аргументов шаблона класса с использованием строковых литералов
При передаче строкового литерала по значению, он преобразуется в const char*, при передаче по ссылке, он преобразуется в cont char[size], т.е. тип T будет иметь фиксированный размер и мы никак не сможем его изменить.

## Определение правила вывода
Можно сделать правила, при котором при передаче строки в стиле C, создает класса std::string

```cpp
MyClass(const char*) -> MyClass<std::string>;//обычно его пишут сразу после определения класса
```
Либо такой пример

```cpp
template<typename T> struct container {  
    template<typename Iter> container(Iter beg, Iter end);  
// и так далее  
};  
// пользовательский хинт для вывода  
template<typename Iter> container(Iter b, Iter e) ->  
	container<typename iterator_traits<Iter>::value_type>;  
std::vector<double> v;  
auto d = container(v.begin(), v.end()); // → container<double>
```

# Вывод типов в шаблонах классов
Первый способ - написать порождающую функцию

```cpp
vector<int> makevector(...){ return { };}
vector<int> v {..};
auto myvector = makevector(v.begin(), next(v.begin(), 5));
```

Начиная с C++17 можно выводить тип с помощью конструктора

```cpp
template<class T>
struct Widget{
	Widget(T value);
};

Widget wint(5); //компилятор ищет нужны конструктор T value = 5; и понимает, что 5 это int, получается Widget<int> wint(5);
```

Если нужно инстанцировать шаблон разными контейнерами

```cpp
template<class Container>
IteratorRange<Container::iterator> Head(Container v, size_t top) {
	return IteratorRange { v.begin (), next(v.begin (), min(top , v.size ())) };
}
```

Может случиться так, что нам передадут const Container, который соответственно будет иметь const_iterator т данный код не будет подходить, поскольку функция короткая, можем изменить тип функции на auto и компилятор будет выводить тип из return.

```cpp
template<class Container>
auto Head(Container v, size_t top) {
	return IteratorRange { v.begin (), next(v.begin (), min(top , v.size ())) 
	};
}
```

Использовать auto в качестве результата функции стоит если:
1. тип результата громоздкий
2. тело функции короткое

При выводе как и с auto срезаются const и ref

```cpp
template <typename T>  
T max(T x, T y) { return x > y ? x : y; }  
const int &b = 1, &c = 2;  
a = max(b, c); // → template<> int max<int>(int, int)  
//Это сделано чтобы уменьшить число неоднозначностей  
int e = 2; int &d = e; // вроде разные типы, но вывод работает  
a = max(d, e); // → template<> int max<int>(int, int)

//поэтому нужно уточнять типы левой ссылкой или указателем
template void foo(T& x);
const int x = 42; 
foo(x); // → template<> void foo(const int& x)
template void bar(const T x);
bar(x); // → template<> void bar(int x)
```

