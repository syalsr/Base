# Универсальные ссылки perfect forwarding
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

То код не будет работать для перемещения, поскольку именованная && это lvalue C++11 предоставляет perfect forwarding - прямую передачу параметров.

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