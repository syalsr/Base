# void_t
```cpp
template <typename...> using void_t = void;
```

`void_t<T, U, V>` означает `void` если все типы легальны и нелегален если нелегален хотя бы один, т.е. это что-то на подобии конъюнкция

Задача: есть две структуры, в одной есть foobar, в другой нет, как определить?

```cpp
struct foo { typedef float foobar; };  
struct bar { };  

std::cout << std::boolalpha << ??? foo << " " << ??? bar;
```

Решение:

```cpp
template <typename, typename = void>  
struct has_typedef_foobar: std::false_type { };  
  
template <typename T>  
struct has_typedef_foobar<T,  
        std::void_t<typename T::foobar>>: std::true_type{};  
  
struct Foo { typedef float foobar; };  
struct Bar {};  
  
int main() {  
    std::cout << std::boolalpha;  
    std::cout << has_typedef_foobar<Foo>{} << std::endl;  
    std::cout << has_typedef_foobar<Bar>{} << std::endl;  
    //true  
    //false
}
```