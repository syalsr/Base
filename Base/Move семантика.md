# Перемещение временных объектов
Суть move семантики заключается в копировании объектов без аллокаций.

lvalue - то у чего есть location
rvalue - то чего нет в памяти(выражение к примеру)

Взять выражение, связать его с именем называется rvalue ref  `int&& y = x +  1`  rvalue ref можно сказать в отличие от lvalue ref дает жизнь rvalue expr. rvalue ref - это lvalue

std::move - берет переменную и кастует к &&

```cpp
int main() {
	int x = 4;
	int &&y = x + 1;
	std::cout << &x << " " << &y << std::endl;
	int &&z = std::move(x);
	z = z + 1;
	y = y + 1;
	std::cout << x << " " << y << std::endl;
	//0xdf539ff8c8 0xdf539ff8cc
	//5 6
}
```

## Кросс-связывание 
Правая ссылка не может быть связана с lvalue 
```cpp
int x = 1; 
int &&y = x + 1; // ok 
int &&b = x; // fail, не rvalue
```

Неконстантная левая ссылка не может быть связана с rvalue 

```cpp
int &c = x + 1; // fail, не lvalue 
const int &d = x + 1; // ok, продляет время жизни
```

Но при этом правая ссылка сама по себе задаёт имя и адрес и является lvalue 

```cpp
int &&e = y; // fail, не rvalue 
int &f = y; // ok
```

Методы над rvalues

```cpp
struct S {  
    int n = 0;  
    int& access() { return n; }  
};  
S x;  
int& y = x.access(); // ok  
int& z = S{}.access(); // UB, висячая ссылка, 
```

Временный объект живет до конца полного выражения, в таких случаях стоит перегружать методы для lvalue и rvalue объектов [[Ссылочные квалификаторы методов]]

```cpp
struct S {  
    int n = 0;  
    int& access() & { return n; }  
};  
S x;  
int& y = x.access(); // ok  
int& z = S{}.access(); // error 
```


```cpp
class X {  
    vector<char> data_;  
public:  
    X() = default;  
    vector<char> const & data() const & { return data_; }  
    vector<char> && data() && { return std::move(data_); }  
};  
X obj;  
vector<char> a = obj.data(); // copy  
vector<char> b = X().data(); // move
```

```cpp
int& foo(int& x) { return x; } // ok  
const int& bar(const int& x) { return x; } // когда как, const ref можно продлять жизнь временным объектам  
int&& buz(int&& x) { return std::move(x); } // DANG, rvalue ref принимает только временные объекты
```

Не всегда стоит использовать move

```cpp
T foo(some arguments) {  
    T x = some expression;  
// more code  
    return std::move(x); // не ошибка, но зачем?  возвращение локального значение это rvalue, move ctor все равно на обратной стороне, если использовать мув отрубится RVO
}
```

```cpp
int x = 1;  
int a = std::move(x);//у базовых типов нет мув ктор, поэтому будет копирование  
assert (x == a); // , поэтому 1 == 1
ScopedPointer y {new int(10)};  
ScopedPointer b = std::move(y);  
assert (y == b); // может выполниться, может не выполниться 
```

Мув конст объекта - это конс &&, то есть можно сказать это const &, произойдет копирование

#do/review 

* [[Функция move]]
* [[rvalue lvalue reference]]
* [[Конструктор перемещения и перемещающий оператор присваивания]]
* [[Передача параметра по значению]]
* [[Move-итераторы]]
* [[Некопируемые типы]]
* [[Copy elision]]
* [[Опасности return]]
* [[exchange]]