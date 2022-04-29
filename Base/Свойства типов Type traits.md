# Свойства типов type traits
```cpp
is_trivially_copyable // побайтово копируемый, memcpy  
is_standard_layout // можно адресовать поля указателем  
is_aggregate // доступна агрегатная инициализация как в C  
is_default_constructible // есть default ctor  
is_copy_constructible, is_copy_assignable  
is_move_constructible, is_nothrow_move_constructible  
is_move_assignable  
is_base_of // B является базой (транзитивно, включая сам тип)  
is_convertible // есть преобразование из A к B
```

Указано не все, проще приложить ссылку https://en.cppreference.com/w/cpp/header/type_traits