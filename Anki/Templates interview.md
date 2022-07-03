#cppanki/templates

Что такое шаблон класса и шаблон функции
?
![[../Base/Шаблоны функций]]
![[../Base/Шаблоны классов]]

Может ли конструктор быть шаблоном функции
?
```cpp
class Base
{
public:
    template<typename T>
    Base(T a) 
    {
        std::cout << a;
    }
};

int main()
{
    Base b{ 2 };//OK
}
```

Может ли виртуальная функция быть шаблонной?
?
Нет
```cpp
class Base
{
public:
    template<typename T>
    virtual void virt(T a) //'void Base::virt(T)' : member function templates cannot be virtual

    {
        std::cout << a;
    }
};
```

Что такое инстанциация шаблона?
?
![[../Base/Инстанцирование]]

Что такое специализация шаблона? Частичная специализация шаблона?
?
![[../Base/Специализация шаблонов классов]]
![[../Base/Частичная специализация шаблонов классов]]

Расскажите об имплементации шаблонных классов в срр-файле?
?
![[../Base/Явное инстанцирование]]

Передача по ссылке или по значению?
?
![[../Base/Передача по ссылке или по значению]]
![[../Base/Универсальные ссылки perfect forwarding]]

Что такое variadic template
?
![[../Base/Вариативные шаблоны variadic templates]]