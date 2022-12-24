# is_reference remove_reference
```cpp
Проверяет, есть ли ссылка
template <typename T> struct is_reference : false_type {};  
template <typename T> struct is_reference<T&> : true_type {};  
template <typename T> struct is_reference<T&&> : true_type {};  

Убирает ссылку если есть
template <typename T> struct remove_reference { using type = T; };  
template <typename T> struct remove_reference<T&> { using type = T; };  
template <typename T> struct remove_reference<T&&> { using type = T; };

template<typename T> 
using remove_reference_t = typename remove_reference<T>::type;
```