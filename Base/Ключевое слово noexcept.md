# Ключевое слово noexcept
Нужно чтобы не позволять функциям и методам выбрасывать исключение. Стоит применять в конструкторах и деструкторах, чтобы объекты корректно создавались и очищались.

noexcept может принимать выражение

```cpp
noexcept(true)//нельзя выбрасывать исключения
noexcept(false)//можно выбрасывать исключения
```

Оператор noexcept возвращает true, если данное выражение помечено, как noexcept

```cpp
void f1() noexcept {}
void f2() noexcept(false) {}
void f3() noexcept(noexcept(f1())) {}
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
