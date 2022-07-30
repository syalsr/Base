# Функциональный объект
Для того, чтобы определить функциональный объект достаточно определить `operator()`
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
template<typename Matcher, typename MatchHandler>
void findMatches(vector<int> values1, vector<int> values2, Matcher matcher, MatchHandler handler)
{
	if (values1.size() != values2.size()) { return; } // Must be same size.
	for (size_t i{ 0 }; i < values1.size(); ++i) {
		if (matcher(values1[i], values2[i])) {
			handler(i, values1[i], values2[i]);
		}
	}
}



class IsLargerThan
{
public:
	explicit IsLargerThan(int value) : m_value{ value } {}
	bool operator()(int value1, int value2) const {
		return value1 > m_value && value2 > m_value;
	}
private:
	int m_value;
};
int main()
{
	vector values1{ 2, 500, 6, 9, 10, 101, 1 };
	vector values2{ 4, 4, 2, 9, 0, 300, 1 };
	findMatches(values1, values2, IsLargerThan{ 100 }, printMatch);
	//Match found at position 5(101, 300)
}
```

## Функциональные объекты стандартной библиотеки
### Арифметические функциональные объекты
C++ предоставляет шаблон класса для 5 арифметических операций: 
1. plus
2. minus
3. multiplies
4. divides
5. modules

```cpp
template <typename Iter, typename StartValue, typename Operation>
auto accumulateData(Iter begin, Iter end, StartValue startValue, Operation op)
{
	auto accumulated { startValue };
	for (Iter iter { begin }; iter != end; ++iter) {
		accumulated = op(accumulated, *iter);
	}
	return accumulated;
}
double geometricMean(vector<int> values)
{
auto mult {accumulateData(cbegin(values), cend(values), 1, multiplies<int>{})};
		return pow(mult, 1.0 / values.size()); // pow() requires <cmath>
}
```

Используя прозрачные функторные операторы можно опустить аргумент типа шаблона `multiplies<>{}`

```cpp
auto mult {accumulateData(cbegin(values), cend(values), 1, multiplies<>{})};//return int
auto mult {accumulateData(cbegin(values), cend(values), 1.1, multiplies<>{})};//return double
```

Рекомендуется всегда использовать transparent operator functors.

### Функциональные объекты сравнения
C++ предоставляет следующие объекты сравнения:
1. equal_to
2. not_equal_to
3. less
4. greater
5. less_equal
6. greater_equal

```cpp
priority_queue<int> myQueue;
myQueue.push(3);
myQueue.push(4);
myQueue.push(2);
myQueue.push(1);
while (!myQueue.empty()) {
	cout << myQueue.top() << " ";//4 3 2 1 
	myQueue.pop();
}
...
priority_queue<int, vector<int>, greater<>> myQueue;
myQueue.push(3);
myQueue.push(4);
myQueue.push(2);
myQueue.push(1);
while (!myQueue.empty()) {
	cout << myQueue.top() << " ";//1 2 3 4  
	myQueue.pop();
}
```

Для контейнеров стандартной библиотеки стоит всегда использовать прозрачные операторы, поскольку в этом случае не будут создаваться лишние копии поскольку происходит гетерогенный поиск, чтобы это не значало

```cpp
class str : public string
{
public:
	str() {}

	str(const char* s)
	{

	}

	void operator=(const char* s)
	{

	}
};
int main()
{
	vector value{ 2, 500, 6, 9, 10, 101, 1 };
	geometricMean(value);

	{
		set<str> mySet;
		auto i1{ mySet.find("Key") }; // string constructed, allocates memory!
		//auto i2{ mySet.find("Key"sv) }; // Compilation error!
	}
	{
		set<str, less<string>> mySet;
		auto i1{ mySet.find("Key") }; // string constructed, allocates memory!
		//auto i2{ mySet.find("Key"sv) }; // Compilation error!
	}
	{
		set<str, less<>> mySet;
		auto i1{ mySet.find("Key") }; // No string constructed, no memory allocated.
		auto i2{ mySet.find("Key"sv) }; // No string constructed, no memory allocated.
	}
}
```

#do/review Почему при использовании прозрачного компаратора при поиске не конструируется объект!!?? https://habr.com/ru/post/523668/

### Логический функциональный объект
C++ предоставляет следующие логические объекты:
1. logical_not
2. logical_and
3. logical_or

Эти логические операции работают только с true, false

```cpp
bool allTrue(const vector<bool>& flags)
{
	return accumulateData(begin(flags), end(flags), true, logical_and<>{});//возвращает true, если все true
}
bool anyTrue(const vector<bool>& flags)
{
	return accumulateData(begin(flags), end(flags), false, logical_or<>{});//возвращает true если хотя бы один true
}
```

### Побитовые функциональные объекты
C++ предоставляет следующие побитовые объекты:
1. bit_and
2. bit_or
3. bit_xor

### Adapter function objects
Когда мы используем стандартные функциональные объект, может возникнуть  несоответствие между сигнатурой и передаваемым объект, чтобы этого избежать нужно использовать adapter function objects.

#### Binders
```cpp
void func(int num, string_view str)
{
	cout << "func(" << num << ", " << str << ")" << endl;
}
int main()
{
	string myString{ "abc" };
	auto f1{ bind(func, placeholders::_1, myString) };//привязываем myString к func, 
	//если мы не привязываем аргументы, мы должны пометить и как placeholers::_1, 
	//_1 - место аргумента в сигнатуре функции
	f1(16);//func(16, abc)
}
```

bind также можно использовать для перестановки аргументов

```cpp
auto f2 { bind(func, placeholders::_2, placeholders::_1) };//первый арг f2 станет вторым, второй первым
f2("Test", 32);//func(32, Test)
```

Когда мы биндим переменную, мы создаем копию, чтобы этого избежать нужно использовать `ref() cref()`

```cpp
void increment(int& value) { ++value; }
int main()
{
	int index{ 0 };
	increment(index);//index==1

	auto incr{ bind(increment, index) };
	incr();//index == 1, не изменился

	auto newincr{ bind(increment, ref(index)) };
	newincr();//index == 2
}
```

Если мы биндим перегруженную функцию, то нужно явно указать какая из перегрузок нам нужна

```cpp
void overloaded(int num) {}
void overloaded(float f) {}

int main()
{
	auto f3{ bind(overloaded, placeholders::_1) }; // ERROR
	auto f4{ bind((void(*)(float))overloaded, placeholders::_1) }; // OK
}
```

Биндим метод класса

```cpp
bool intEqual(int item1, int item2)
{
	return item1 == item2;
}
template<typename Matcher, typename MatchHandler>
void findMatches(vector<int> values1, vector<int> values2, Matcher matcher, MatchHandler handler)
{
	if (values1.size() != values2.size()) { return; } // Must be same size.
	for (size_t i{ 0 }; i < values1.size(); ++i) {
		if (matcher(values1[i], values2[i])) {
			handler(i, values1[i], values2[i]);
		}
	}
}
class Handler
{
public:
	void handleMatch(size_t position, int value1, int value2)
	{
		cout << "Match found at position " << position
			<< "(" << value1 << ", " << value2 << ")" << endl;
	}
};

int main()
{
	vector values1{ 2, 500, 6, 9, 10, 101, 1 };
	vector values2{ 4, 4, 2, 9, 0, 300, 1 };
	Handler handler;
	findMatches(values1, values2, intEqual, bind(&Handler::handleMatch, &handler,
		placeholders::_1, placeholders::_2, placeholders::_3));
}
/*
	Match found at position 3(9, 9)
	Match found at position 6(1, 1)
*/
```

Биндим стандартный функциональный объект

```cpp
int main()
{
	auto greaterEqualTo100{ bind(greater_equal<>{}, placeholders::_1, 100) };
	auto f = greaterEqualTo100(12);//false
	auto f1 = greaterEqualTo100(120);//true
}
```

#### Negator
not_fn() - это так называемый negator, инвертирует результат

Если хотим использовать `findmaches()`для поиска неравных пар можем применить негатор к `intequal()`

```cpp
int main()
{
	vector values1{ 2, 500, 6, 9, 10, 101, 1 };
	vector values2{ 4, 4, 2, 9, 0, 300, 1 };
	findMatches(values1, values2, not_fn(intEqual), printMatch);
}
/*
	Match found at position 0(2, 4)
	Match found at position 1(500, 4)
	Match found at position 2(6, 2)
	Match found at position 4(10, 0)
	Match found at position 5(101, 300)
*/
```

### Вызов метода
```cpp
template <typename Matcher>
void printMatchingStrings(const vector<string>& strings, Matcher matcher)
{
	for (const auto& string : strings) {
		if (matcher(string)) { cout << string << " "; }
	}
}


int main()
{
	vector<string> values{ "Hello", "", "", "World", "!" };
	printMatchingStrings(values, not_fn(mem_fn(&string::empty)));
}
```

#do/review  Для чего mem_fn? если убрать результат такой же