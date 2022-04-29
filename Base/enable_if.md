# enable_if
Позволяет игнорировать шаблоны при определенных компайл тайм условиях.

```cpp
template<typename T>
typename std::enable_if<(sizeof(T) > 4)>::type
foo() {}

template<typename T>
std::enable_if_t<(sizeof(T) > 4)>
foo() {}
```

Это определение foo<>() игнорируется, если условие `sizeof(T)>4` дает false, если `true`, то шаблон раскрывается до 

```cpp
void foo() {}
```

1. Если выражение вычисляется как true, член type дает тип
	1. void, если второй аргумент не передан
	2. иначе второй аргумент шаблона
2. Если выражение вычисляется как false, член type не определен. Благодаря SFINAE, шаблон функции с выражением `enable_if` в этом случае игнорируется.

```cpp
//второй вариант написания
template<typename T, typename = std::enable_if_t<(sizeof(T) > 4)>>
void foo() {}

//третий вариант написания
template<typename T>
using EnableIfSizeGreater4 = std::enable_if_t<(sizeof(T) > 4)>;
template<typename T, typename = EnableIfSizeGreater4<T>>
void foo() {}
```

//ВЛадимиров
Модернизированный void_t.  Хотим чтобы foo было валидно только для input итераторов:

```cpp
template<typename It>
using iterator_category_t = typename std::iterator_traits<IT>::iterator_category;

template<typename It, typename T = std::enable_if_t<std::is_base_of_v<input_iterator_tag,
																	iterator_category_t<It>>>>
void foo(It first, It last)
{}
```

```cpp
template <typename It>  
using iterator_category_t = typename std::iterator_traits<It>::iterator_category;  
  
template <typename It, typename T = std::enable_if_t<  
        std::is_base_of_v<std::random_access_iterator_tag,  
                iterator_category_t<It>>  
>>  
void foo(It first, It last) {  
    std::cout << std::distance(first, last) << std::endl;  
}  
  
int main() {  
    std::vector v{1, 2, 3};  
    foo(v.begin(), v.end());//3  
    std::list l{1, 2, 3};  
    foo(l.begin(), l.end());//error  
    //no matching function for call to    
    // foo(std::__cxx11::list<int, std::allocator<int> >::iterator,    
    //      std::__cxx11::list<int, std::allocator<int> >::iterator)    
    //no type named ‘type’ in ‘struct std::enable_if<false, void>’    
    //using enable_if_t = typename enable_if<_Cond, _Tp>::type;
}
```