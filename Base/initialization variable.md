```cpp
int init = 7; // default
int init { 7 }; // after C++ 11, better than default
int init { }; // zero initialization
int init; // value is unknown
```
Инициализация с помощью фигурных скобок называется uniform, ее преимущество в том, что она будет выдавать ошибку если будет происходить потеря данных(присваиваем double int'y).