# SFINAE - Substitution Failure Is Not An Error 
Провал подстановки не является ошибкой. Если в результате подстановки в непосредственном контексте класса (функции, алиаса, переменной) возникает невалидная конструкция, эта подстановка неуспешна, но не ошибочна.

Подстановка должна быть провалена в объявлении:

```cpp
int negate (int i) { return -i; } 
template<typename T> T negate(const T& t) { 
	typename T::value_type n = -t(); //в перегрузке выигрывает эта функция, подстановка состоялась, но у double нет вложенного типа value_type, подстановка состоялась, это не SFINAE 
} 
negate(2.0); // ошибка второй фазы
```

Допустим у нас есть 2 типа и мы хотим знать равны ли они:

```cpp
template int foo() { 
	// как вернуть 1 если T == U и 0 если нет? 
}
```

Это задача отображения из типов на числа - называется интегральной константой:

```cpp
template <typename T, T v> struct integral_constant {  
    static const T value = v;  
    typedef T value_type;  
    typedef integral_constant type;  
    operator value_type() const { return value; }  //оператор приведения типов
};

using ic6 = integral_constant<int, 6>; 
auto n = 7 * ic6{};
```

Зная это решим задачу:

```cpp
using true_type = integral_constant<bool, true>; //из std
using false_type = integral_constant<bool, false>;//из std

namespace s  
{  
    template<typename T, typename U>  
    struct is_same : std::false_type //публичное наследование  
    {};  
  
    template<typename T>  
    struct is_same<T, T> : std::true_type //публичное наследование  
    {}; // для T == T  
  
    template<typename T, typename U>  
    using is_same_t = typename is_same<T, U>::type;  
  
}  
  
int main() {  
    std::cout << std::boolalpha;  
    std::cout << s::is_same_t<int, int>{} << std::endl;  
    std::cout << s::is_same_t<int, char>{} << std::endl;  
}
```
