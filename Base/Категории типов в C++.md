# Категории типов в C++
Любой тип в языке C++ попадает под одну из следующих категорий:

```cpp
is_void  
is_null_pointer//именно тип nullptr_t  
is_integral, is_floating_point // для T и для cv T& транзитивно  
is_array; // только встроенные, не std::array  
is_pointer; // включая указатели на обычные функции  
is_lvalue_reference, is_rvalue_reference  
is_member_object_pointer, is_member_function_pointer  
is_enum, is_union, is_class  
is_function // обычные функции

std::cout << std::boolalpha << std::is_void::value << '\n';
```