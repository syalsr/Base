# Function pointers
Каждая функция имеет свой адрес, соответственно мы можем использовать функцию как переменную, чтобы например передать ее в другую функцию. Функции которые принимают функции или возвращают их, называются функциями высшего порядка.

```cpp
using Matcher = bool(*)(int, int);
using MatchHandler = void(*)(size_t, int, int);
bool intEqual(int item1, int item2) 
{ 
	return item1 == item2; 
}

void printMatch(size_t position, int value1, int value2)
{
	cout << "Match found at position " << position 
			<< "(" << value1 << ", " << value2 << ")" << endl;
}
void findMatches(vector<int> values1, vector<int> values2, Matcher matcher, MatchHandler handler)
{
	if (values1.size() != values2.size()) { return; } // Must be same size.
	for (size_t i{ 0 }; i < values1.size(); ++i) {
		if (matcher(values1[i], values2[i])) {
			handler(i, values1[i], values2[i]);
		}
	}
}



int main()
{
	vector<int> v = { 1,2,3 };
	vector<int> vv = { 1,2,1 };
	findMatches(v, vv, &intEqual, &printMatch);
	//Match found at position 0(1, 1)
	//Match found at position 1(2, 2)
}
```

Преимущество в том, что мы можем передавать разные адреса функций которые реализованы по разному. К примеру, могли бы по разному сравнить вектора

```cpp
bool bothOdd(int item1, int item2) { return item1 % 2 == 1 && item2 % 2 == 1; }
```

Обычно, указатель на функцию используют, чтобы получить указатель на функцию в динамической библиотеке.

# Pointer to methods and data members
Мы не можем иметь указатель на статический метод/данные, потому что мы выполняем операции в контексте объекта.
```cpp
int (Employee::*methodPtr) () const { &Employee::getSalary };//определяем переменную methodPtr типа указаль на нестатический метод класса Empoyee::getSalary() который возвращает int
Employee employee { "John", "Doe" };
cout << (employee.*methodPtr)() << endl;//поскольку operator() имеет более высокий приоритет, чем * оборачиваем все в скобки

Employee* employee { new Employee { "John", "Doe" } };
cout << (employee->*methodPtr)() << endl;
```

Определим methodPtr через alias и auto

```cpp
using PtrToGet = int (Employee::*) () const;
PtrToGet methodPtr { &Employee::getSalary };
Employee employee { "John", "Doe" };
cout << (employee.*methodPtr)() << endl;

auto methodPtr { &Employee::getSalary };
Employee employee { "John", "Doe" };
cout << (employee.*methodPtr)() << endl;
```

Указатели на методы и данные обычно не используются, но стоит помнить, что разыменовывать указатель на не статический метод или данные нельзя без объекта. 

# std::function
Является полиморфной оберткой функции которая используется для создания типа который может указывать на что-либо вызываемое - функцию, функциональный объект, лямбда выражение.

```cpp
void func(int num, string_view str)
{
	cout <<  num << " " << str << endl;
}
int main()
{
	function<void(int, string_view)> f1{ func };//с помощью CTAD можно так function f1 { func };
	f1(1, "test");
	auto f1 { func };//при таком использовании тип f1 будет указателем на функцию, а не std::function
}
```

```cpp
using Matcher = function<bool(int, int)>;
using MatchHandler = function<void(size_t, int, int)>;
bool intEqual(int item1, int item2) 
{ 
	return item1 == item2; 
}

void printMatch(size_t position, int value1, int value2)
{
	cout << "Match found at position " << position 
			<< "(" << value1 << ", " << value2 << ")" << endl;
}
void findMatches(vector<int> values1, vector<int> values2, Matcher matcher, MatchHandler handler)
{
	if (values1.size() != values2.size()) { return; } // Must be same size.
	for (size_t i{ 0 }; i < values1.size(); ++i) {
		if (matcher(values1[i], values2[i])) {
			handler(i, values1[i], values2[i]);
		}
	}
}



int main()
{
	vector<int> v = { 1,2,3 };
	vector<int> vv = { 1,2,1 };
	findMatches(v, vv, &intEqual, &printMatch);
	//Match found at position 0(1, 1)
	//Match found at position 1(2, 2)
}
```