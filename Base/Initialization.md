# uniform
```cpp
int init = 7; // default
int init { 7 }; // after C++ 11, better than default
int init { }; // zero initialization
int init; // value is unknown
```
Инициализация с помощью фигурных скобок называется uniform, ее преимущество в том, что она будет выдавать ошибку если будет происходить потеря данных(присваиваем double int'y).

# Copy list and Direct list initialization
```cpp
/*Copy list initialization:*/ T obj = {arg1, arg2, ...};
/*Direct list initialization:*/ T obj {arg1, arg2, ...};

//с++17
// Copy list initialization
auto a = { 11 }; // initializer_list<int>
auto b = { 11, 22 }; // initializer_list<int>
// Direct list initialization
auto c { 11 }; // int
auto d { 11, 22 }; // Error, too many elements.

//с++11/14
// Copy list initialization
auto a = { 11 }; // initializer_list<int>
auto b = { 11, 22 }; // initializer_list<int>
// Direct list initialization
auto c { 11 }; // initializer_list<int>
auto d { 11, 22 }; // initializer_list<int>

struct X
{
	int y;
	double x;
}
X x{1, 2.0};//ok, агрегатная инициализация, если мы добавим поле private, то это будет ошибка и для инициализации нам нужно будет создавать конструктор

struct X
{
    int y;
    double x;
    private:
    int z;
};

int main() {
    X x{1, 2.0};//error, добавим конструктор и тогда фигурные скобки буду означать его вызов
}
```

При copy initialization все элементы должны иметь один тип

#do/review возможно написать про остальные типы инициализации