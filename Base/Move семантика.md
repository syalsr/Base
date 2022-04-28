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

# Связывание ссылок
```cpp
int x = 1;
int& a = x;
int const& b = x;
int const& c = x + 1;//продлили время жизни временного объекта
x = a + b + c;
cout << a << b << c;//4 4 2
```

```cpp
int x = 1; 
int &a = x; 
int const &c = x + 1; 
int &&d = x + 1; 
c += 1; // fail 
d += 1; // ok
```

![[../Files/Pasted image 20220426180453.png]]

# Кросс-связывание
```cpp
//Правая ссылка не может быть связана с lvalue
int x = 1;
int&& y = x * 3; // ok
int&& b = x; // fail, не rvalue

//Неконстантная левая ссылка не может быть связана с rvalue
int &c = x * 3; // fail, не lvalue 
const int &d = x * 3; // ok, продляет время жизни

//Но при этом правая ссылка сама по себе задаёт имя и адрес и является lvalue
int &&e = y; // fail, не rvalue 
int &f = e; // ok
```

# Перегрузка функций по ссылкам
```cpp
int foo(int& p); // 1
int foo(int&& p); // 2

int x = 1;
foo(x); // 1
foo(x + 0); // 2
foo(foo(x)); // 1 then 2
```

# Висячие ссылки dangling reference
```cpp
const int& clref (int p) { return p + 0; } // p + 0 is dead 

int x = clref(2); // значение x не определено 
const int& cx = clref(3); // значение cx не определено

//Провиснуть могут все типы ссылок
const int& clref(int p) { return p + 0; } // p + 0 is dead
const int& clref(int p) { return p; } // p rests in peace
int& lref(int p) { return p; } // p is p no more
int&& rref(int p) { return p + 0; } // p + 0 has expired and gone
int&& rvref(int&& p) { return p + 0; } // p + 0 is an ex-p + 0

//• Есть неожиданно корректные случаи, когда identity сохраняется в фрейме вызова
int& lvref(int& x) { return x; } // ok
const int& clvref(const int& x) { return x; } // ok
```

Как связаться с lvalue?

```cpp
int x = 42;
int&& rvx = std::move(x);
rvx += 2;
assert(x == 44);
```

![[../Files/Pasted image 20220426181353.png]]

```cpp
struct RBind {  
    int& ref;  
    RBind(int&& r) : ref(r) {}  
    RBind const& plus(int x) const { ref += x; return *this; }  
    RBind const& mult(int x) const { ref *= x; return *this; }  
    int get() const { return ref; }  
};  
int x = 1;  
cout << RBind(1).plus(2).mult(3).get() << endl; // 9  
cout << RBind(move(x)).plus(2).mult(3).get() << endl; // 9  
cout << RBind(x + 0).plus(2).mult(3).get() << endl; // 33
```

Конструктор берущий rvalue ref не обязан сохранять значение (т.к. это rvalue)

```cpp
class Buffer {  
    size_t sz; int *buf;  
public:  
    Buffer (Buffer&& rhs) : sz(rhs.sz), buf(rhs.buf) {  
        rhs.sz = 0; rhs.buf = nullptr;  
    }  
    Buffer (const Buffer& rhs) : sz(rhs.sz), buf(new int[sz]) {  
        copy (rhs.buf, rhs.buf + sz, buf);  
    }  
};
```

Конструктор копирования (аналогично ведёт себя копирующее присваивание) 
1. deleted если его нет, но есть пользовательское перемещение или move ctor 
2. иначе defaulted если его нет (но если при этом есть деструктор или парный копирующий метод, то это deprecated)

Перемещающий конструктор (аналогично перемещающее присваивание) 
1. deleted если его нет, но при этом есть парный перемещающий метод или copy ctor или копирующее присваивание или деструктор 
2. иначе defaulted если его нет


# Результаты функци rvalues
```cpp
Buffer foo (void) {  
    Buffer retval;  
// какое-то заполнение retval  
    return retval;  
}  
Buffer t = foo(); // t будет move-constructed
```