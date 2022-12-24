# Consepts
Концепты позволяют формулировать требования/условия для шаблонов.

```cpp
template<typename STR>
requires std::is_convertible_v<STR,std::string>
Person(STR&& n) : name(std::forward<STR>(n)) {
	...
}

template<typename T>
concept ConvertibleToString = std::is_convertible_v<T,std::string>;

template<typename STR>
requires ConvertibleToString<STR>
Person(STR&& n) : name(std::forward<STR>(n)) {
...
}

template<ConvertibleToString STR>
Person(STR&& n) : name(std::forward<STR>(n)) {
...
}
```