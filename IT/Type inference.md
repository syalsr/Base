# Type inference
Вывод типа позволяют компилятору самому определять тип выражения, функции, метода, объекта.

# auto - режет ссылки и константность
Выводит типы как и [[Templates#Вывод типов шаблонами]]

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

const int &x = 42; 

auto y = x; // → int y = x; 
auto& z = x; // → const int& z = x;

int const * const x = 42; 
auto y = x; // → int const *y = x; 
auto *z = x; // → int const *const z = x;
```


# decltype
decltype существует в двух основных видах - для имени и для выражения 
1. decltype(name) выводит тип с которым было объявлено имя 
2. decltype(expression) работает чуточку сложнее 
	1. decltype(lvalue) это тип выражения + левая ссылка 
	2. decltype(xvalue) это тип выражения + правая ссылка 
	3. decltype(prvalue) это тип выражения

```cpp
int a[10];
decltype(a[0]) b = a[0];//int& b

int x { 123 };  
decltype(x) y { x };//int y
decltype((x)) y { x };//int& y

Не стоит использовать auto без ->decltype, иначе будет не ясен тип функции

template <typename T>  
auto makeAndProcessObject(const T& builder) ->  
decltype (builder.makeObject()) {  
    auto val = builder.makeObject();  
	// что-то делаем с val  
    return val;  
}
```

```cpp
template <typename T> foo(T x);  
const int &t;  
foo(t); // → foo<int>(int x)  
auto s = t; // → int s  
//Для точного вывода существует decltype  
decltype(t) u = 1; // → const int& u


struct Point { int x, y; }; 

Point porig {1, 2}; 
const Point &p = porig;

decltype(p.x) x = 0; // → int x = 0;
decltype((p.x)) x = 0; // → const int &x = 0;
decltype((porig)) x = porig; // → Point& x = porig;
```

Если в decltype(expr) оказывается, что expr это lvalue, то decltype(expr) добавляет lvalue reference к выведенному типу

```cpp
int x[10];
x[5] = 4; // здесь x[5] работает как ссылка
decltype(x[5]) y = x[5]; // → int& y = x[5];
y = 4; // изменяет x[5]
```

# decltype(auto)
Выводит из правой части, но по правилам decltype

```cpp
double x = 1.0; 
decltype(x) tmp = x; // два раза x не нужен 
decltype(auto) tmp = x; // это именно то, что нужно
decltype(auto) tmp = x; // → double 
decltype(auto) tmp = (x); // → double&

// example by Scott Meyers
decltype(auto) lookupValue(int idx) {
	auto ind = calcValue(idx);
	auto val = values[ind]; // → int val
	return (val); // → int& в качестве результата
	return val;// → int в качестве результата
}
```


# Форсированная деградация decay_t
```cpp
const int& x = 0;
auto y = x; // x decays to int
decay_t<decltype(x)> y; // decltype(x) forced decays to int
```