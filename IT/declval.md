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