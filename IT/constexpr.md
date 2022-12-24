# constexpr
Это ключевое слово означает, что значение функции, метода переменной будет посчитано на этапе компиляции.

```cpp
const int getArraySize() { return 32; }
int main()
{
	int myArray[getArraySize()]; // Invalid in C++
}

constexpr int getArraySize() { return 32; }
int main()
{
	int myArray[getArraySize()]; // OK
}
```

`constexpr` функции могут вызывать другие `constexpr` функции, но не  `non-conexpr`.