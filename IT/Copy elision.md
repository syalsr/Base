# Copy elision
Выражение вида `return object` является rvalue выражением, если объект является локальной переменной, параметром функции или временным значением, то можем произойти 
1. RVO - return value optimization - возвращение из функции временного объекта 
2. NRVO - named return value optimization - возврат из функции локальной переменной

```cpp
struct T {
	T() {}
	T(T&&) noexcept { cout << "move" << endl; }
	T(const T&) { cout << "copy" << endl; }
};

T f() {
	T t;
	return t; //вызов T::T(T&&)
}

T h() {
	return T(); //ничего
}

T g(T t) {
	return t; //вызов T::T(T&&)
}
```

Используя [[Функция move|return std::move(object)]] компилятор не применяет copy elision, он будет использовать либо move семантику, либо копирующую семантики, в зависимости от того, что определено в классе. Так что не стоит использовать move вместе с return.

В данном случае компилятор будет использовать конструктор копирования, поскольку это не выражение вида return object

```cpp
return condition ? object1 : object2;
```

Лучше использовать это:

```cpp
if (condition) {
	return object1;
} else {
	return object2;
}
```
Либо это
```cpp
return condition ? std::move(object1) : std::move(object2);
```