# if constexpr
Позволяет отключение блоки if/else во время компиляции.

```cpp
template<typename T, typename... Types>
void print (T const& firstArg, Types const&... args)
{
	std::cout << firstArg << '\n';
	if constexpr(sizeof...(args) > 0) {
		print(args...); // code only available if sizeof...(args)>0 (since C++17)
	}
}
```

Когда мы передаем последний аргумент, в пачке остается 0 элементов и условие не выполняется, выход из функции. Если убрать constexpr, то условие будет выполняться во время выполнения, будет ошибка, поскольку у компилятора нет функции `void print(){}`.