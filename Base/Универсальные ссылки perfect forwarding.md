# Семантика перемещения
Нужно уменьшить этот код используя шаблоны
```cpp
class X {  
    ...  
};  
void g (X&) {  
    std::cout << "g() for variable\n";  
}  
  
void g (X const&) {  
    std::cout << "g() for constant\n";  
}  
void g (X&&) {  
    std::cout << "g() for movable object\n";  
}  
// let f() forward argument val to g():  
void f (X& val) {  
    g(val); // val is non-const lvalue => calls g(X&)  
}  
void f (X const& val) {  
    g(val); // val is const lvalue => calls g(X const&)  
}  
void f (X&& val) {  
    g(std::move(val)); // val is non-const lvalue => needs std::move() to call g(X&&)  
}  
int main()  
{  
    X v; // create variable  
    X const c; // create constant  
    f(v); // f() for nonconstant object calls f(X&) => calls g(X&)  
    f(c); // f() for constant object calls f(X const&) => calls g(X const&)  
    f(X()); // f() for temporary calls f(X&&) => calls g(X&&)  
    f(std::move(v)); // f() for movable variable calls f(X&&) => calls g(X&&)  
}
```

Если сделать так:

```cpp
template<typename T>
void f (T val) {
	g(T);
}
```

То код не будет работать для перемещения, поскольку именнованная && это lvalue C++11 предоставляет perfect forwarding - прямую передачу параметров.

```cpp
class X {  
    ...  
};  
void g (X&) {  
    std::cout << "g() for variable\n";  
}  
void g (X const&) {  
    std::cout << "g() for constant\n";  
}  
void g (X&&) {  
    std::cout << "g() for movable object\n";  
}  
// let f() perfect forward argument val to g():  
template<typename T>  
void f (T&& val) {  
    g(std::forward<T>(val)); //ведет себя по разному, в отличии от передаваемого значения 
}  
int main()  
{  
    X v; // create variable  
    X const c; // create constant  
    f(v); // f() for variable calls f(X&) => calls g(X&)  
    f(c); // f() for constant calls f(X const&) => calls g(X const&)  
    f(X()); // f() for temporary calls f(X&&) => calls g(X&&)  
    f(std::move(v)); // f() for move-enabled variable calls f(X&&) => calls g(X&&)  
}
```

## Шаблоны специальных методов
```cpp
class Person  
{  
private:  
    std::string name;  
public:  
// constructor for passed initial name:  
    explicit Person(std::string const& n) : name(n) {  
        std::cout << "copying string-CONSTR for '" << name << "'\n";  
    }  
    explicit Person(std::string&& n) : name(std::move(n)) {  
        std::cout << "moving string-CONSTR for '" << name << "'\n";  
    }  
// copy and move constructor:  
    Person (Person const& p) : name(p.name) {  
        std::cout << "COPY-CONSTR Person '" << name << "'\n";  
    }  
    Person (Person&& p) : name(std::move(p.name)) {  
        std::cout << "MOVE-CONSTR Person '" << name << "'\n";  
    }  
};  
int main()  
{  
    std::string s = "sname";  
    Person p1(s); // init with string object => calls copying string-CONSTR  
    Person p2("tmp"); // init with string literal => calls moving string-CONSTR  
    Person p3(p1); // copy Person => calls COPY-CONSTR  
    Person p4(std::move(p1)); // move Person => calls MOVE-CONST  
}
```

Используя perfect forwarding может из 2 конструкторов сделать 1

```cpp
class Person  
{  
private:  
    std::string name;  
public:  
// generic constructor for passed initial name:  
    template<typename STR>  
    explicit Person(STR&& n) : name(std::forward<STR>(n)) {  
        std::cout << "TMPL-CONSTR for '" << name << "'\n";  
    }  
// copy and move constructor:  
    Person (Person const& p) : name(p.name) {  
        std::cout << "COPY-CONSTR Person '" << name << "'\n";  
    }  
    Person (Person&& p) : name(std::move(p.name)) {  
        std::cout << "MOVE-CONSTR Person '" << name << "'\n";  
    }  
};

int main()
{
	std::string s = "sname";
	Person p1(s); // init with string object => calls TMPL-CONSTR
	Person p2("tmp"); // init with string literal => calls TMPL-CONSTR
}
```

```cpp
Person p3(p1); // ERROR
Person p4(std::move(p1)); // OK: move Person => calls MOVE-CONST
Person const p2c("ctmp"); // init constant object with string literal
Person p3c(p2c); // OK: copy constant Person => calls COPY-CONSTR
```

Копирование non const объекта не удалось, потому что в данном случае вызывается

```cpp
explicit Person(STR&& n) : name(std::forward<STR>(n)) {  
    std::cout << "TMPL-CONSTR for '" << name << "'\n";  
}
```

А не предполагаемый конструктор копирования, STR заменяется типом `Person&`

Используем enable_if для решения

```cpp
template<typename STR,
typename = std::enable_if_t<std::is_convertible_v<STR, std::string>>>//если параметр шаблон может быть преобразован в string
Person(STR&& n);

//если да, то раскрывается до
template<typename STR, typename = void>
Person(STR&& n);

//если нет весь шаблон функции игнорируется
template<typename T>
using EnableIfString = std::enable_if_t<std::is_convertible_v<T, std::string>>;
template<typename STR, typename = EnableIfString<STR>>
Person(STR&& n);
```

В результате:

```cpp
template<typename T>  
using EnableIfString = std::enable_if_t<std::is_convertible_v<T,std::string>>;  
class Person  
{  
private:  
    std::string name;  
public:  
// generic constructor for passed initial name:  
    template<typename STR, typename = EnableIfString<STR>>  
    explicit Person(STR&& n)  
            : name(std::forward<STR>(n)) {  
        std::cout << "TMPL-CONSTR for '" << name << "'\n";  
    }  
    // copy and move constructor:  
    Person (Person const& p) : name(p.name) {  
        std::cout << "COPY-CONSTR Person '" << name << "'\n";  
    }  
    Person (Person&& p) : name(std::move(p.name)) {  
        std::cout << "MOVE-CONSTR Person '" << name << "'\n";  
    }  
};

int main()
{
	std::string s = "sname";
	Person p1(s); // init with string object => calls TMPL-CONSTR
	Person p2("tmp"); // init with string literal => calls TMPL-CONSTR
	Person p3(p1); // OK => calls COPY-CONSTR
	Person p4(std::move(p1)); // OK => calls MOVE-CONST
}
```

Вместо is_convertible(типы неявно преобразуются) можно использовать is_constructible, в этом случае порядок аргументов противоположный.

#do/start почитать про их разницу и применения.

# forward
```cpp
template<typename Fun, typename Args>  
decltype(auto) transparent(Fun fun, Args&& args) {  
    return fun(forward<Args>(args));  
}
```

1. Используем decltype(auto), чтобы не срезать тип
2. Args&& используем свертку ссылок
3. forward в зависимости от того какой тип, мувает либо передает ссылку. Если у нас передали lvalue ref то forward не влияет, если передаем rvalue ref или rvalue мувается

```cpp
```cpp
template<typename Fun, typename... Args>  
decltype(auto) transparent(Fun fun, Args&&... args) {  
    return fun(forward<Args>(args)...);  
}
```