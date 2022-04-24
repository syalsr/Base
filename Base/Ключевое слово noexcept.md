# Ключевое слово noexcept
Даем обещание компилятору, что функция не выбросит исключения благодаря чему компилятор может как-то соптимизировать код. Если виртуальная функция noexcept, то все ее переопределения тоже noexcept. Деструктор по умолчанию noexcept. Стоит применять в конструкторах и деструкторах, чтобы объекты корректно создавались и очищались.

Если из noexcept функции все-таки выбросить исключение вызовется `abort()`

`=default` методы помечаются `noexcept`, у наследников также noexcept даже если не определяем

# Оператор noexcept
Оператор оценивает выражение, но не вычисляет его, он смотрит помечена ли функция как noexcept, если да, то возвращает true.

```cpp
void f1() noexcept {}
void f2() noexcept(false) {}
void f3() noexcept(noexcept(f1())) {}//первый noexcept - аннотация, вложенный, второй - оператор
void f4() noexcept(noexcept(f2())) {}
int main()
{
	std::cout << boolalpha 
		<< noexcept(f1()) << "\n"//вызываемая функция помеченна noexcept
		<< noexcept(f2()) << "\n"//вызываемая функция помеченна noexcept(false)
		<< noexcept(f3()) << "\n"//и вызываемая функция f3 и функция которая вызывается внутри f1 noexcept
		<< noexcept(f4());//Вызываемая функция noexcept, но f2 помечена noexxcept(false)
	/*
		true
		false
		true
		false
	*/
}
```

Т.е. аннотация - обещание, оператор - проверка обещания
# Когда его использовать
Когда функция работает с фундаментальными типами, они точно не бросают исключения

```cpp
template <class T>  
T copy(T const& orig) noexcept(is_fundamental<T>::value) {  
    return orig;  
}
```

#do/important_start https://arne-mertz.de/2016/01/modern-c-features-keyword-noexcept/
#do/review 