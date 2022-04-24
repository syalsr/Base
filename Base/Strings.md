# String


Что не так с [[C style string|сишными строками]]?

Класс string, основан на динамическом массиве char, сам выделяет нужную память и сам ее удаляет, когда объект выходит из области видимости.

Полезные методы:
1. substr - возвращает подстроку
2. find - возвращает либо позицию символа, либо npos
3. starts_with(str)/ends_with(str) - возвращает true, если наша строка начинается/заканчивается с данной подстрокой.
4. c_str - возвращает const char*

```cpp
auto string1 { "Hello World" }; // string1 is a const char*.
auto string2 { "Hello World"s }; // string2 is an std::string.

string myString;
myString.insert(cend(myString), 'h');
myString.insert(cend(myString), 'e');
myString.push_back('l');
myString.push_back('l');
myString.push_back('o');
for (const auto& letter : myString) {
	cout << letter;
}
cout << endl;
for (auto it { cbegin(myString) }; it != cend(myString); ++it) {
	cout << *it;
}
cout << endl;
```

## [[Литералы]]

## Преобразования
### High-level
`to_string(val)` преобразует число в строку

Следующие функции конвертируют из строки число, base означает основание: 10 - десятичное(0-9), 16 - шестнадцатеричное(0-E)
```cpp
	1. int stoi(const string& str, size_t *idx=0, int base=10);
	2. long stol(const string& str, size_t *idx=0, int base=10);
	3. unsigned long stoul(const string& str, size_t *idx=0, int base=10);
	4. long long stoll(const string& str, size_t *idx=0, int base=10);
	5. unsigned long long stoull(const string& str, size_t *idx=0, int base=10);
	6. float stof(const string& str, size_t *idx=0);
	7. double stod(const string& str, size_t *idx=0);
	8. long double stold(const string& str, size_t *idx=0);
```

### Low-level
Такие функции не работают напрямую с std::string и не выполняют выделений памяти, могут работать быстрее, чем high-level функции. Также при конвертации строки в число и обратно, мы получим точно такое же значение, без потери точности.

#### Converting to strings
```cpp
struct to_chars_result {
	char* ptr;
	errc ec;
};

to_chars_result to_chars(char* first, char* last, IntegerT value, int base = 10);
```

1. IntegerT - любое знаковый/беззнаковый целый тип или символ
2. ptr - равен указателю на конец записанных символов, если преобразование успешно, либо равен last, если преобразование не удалость(т.е. `ec == errc::value_too_large`)

```cpp
const size_t BufferSize { 50 };
string out(BufferSize, ' '); //поскольку не знаем какой длины будет число инициализируем 50 пробелами
auto [ptr, error] { to_chars(out.data(), out.data() + out.size(), 12345) };
if (error == errc{}) { cout << out << endl; /* Conversion successful. */ }
//В out хранится 12345 и 45 пробелов
```

```cpp
enum class chars_format {
	scientific, // Style: (-)d.ddde±dd
	fixed, // Style: (-)ddd.ddd
	hex, // Style: (-)h.hhhp±d (Note: no 0x!)
	general = fixed | scientific // See next paragraph.
};

to_chars_result to_chars(char* first, char* last, FloatT value);
to_chars_result to_chars(char* first, char* last, FloatT value,
chars_format format);
to_chars_result to_chars(char* first, char* last, FloatT value,
chars_format format, int precision);
```

FloatT может быть float, double, long double.

```cpp
const size_t BufferSize { 50 };
double value { 0.314 };
string out(BufferSize, ' '); // A string of BufferSize space characters.
auto [ptr, error] { to_chars(out.data(), out.data() + out.size(), value) };
if (error == errc{}) { cout << out << endl; /* Conversion successful. */ }
```

#### Converting from strings
```cpp
struct from_chars_result {
	const char* ptr;
	errc ec;
};
from_chars_result from_chars(const char* first, const char* last,
IntegerT& value, int base = 10);
from_chars_result from_chars(const char* first, const char* last,
FloatT& value,
chars_format format = chars_format::general);
```

`ptr` - указатель на первый символ который не был преобразован или он равен last, если все символы были конвертированы. Если ни один символ не был конвертирован, то `ptr == first` и `ec == errc::invalid_argument`, если переданное значение слишком больше для конвертирование его в данный тип, то `ec == errc::result_out_of_range`, `from_chars()` не пропускает пробельные символы.

```cpp
const size_t BufferSize{ 50 };
	double value1{ 0.314 };
	string out(BufferSize, ' '); // A string of BufferSize space characters.
	auto [ptr1, error1] { to_chars(out.data(), out.data() + out.size(), value1) };
	if (error1 == errc{}) { cout << out << endl; /* Conversion successful. */ }
	double value2;
	auto [ptr2, error2] { from_chars(out.data(), out.data() + out.size(), value2) };
	if (error2 == errc{}) {
		if (value1 == value2) {
			cout << "Perfect roundtrip" << endl;
		}
		else {
			cout << "No perfect roundtrip?!?" << endl;
		}
	}
```

# String_view
`string_view` – это ссылка на где-то хранящийся диапазон символов(он работает не с итераторами, а с позициями). Допустим нам дана строка состоящая из пробелов и слов, нужно написать функцию которая возвращает вектор из этих строк

```cpp
string_view LeftSpace(string_view& s)
{
	while(!s.empty() && isspace(s[0]))
		s.remove_prefix(1);
}
vector<string_view> SplitIntoWords(string_view s)
{
	vector<string_view> result;
	LeftSpace(s); //убираем пробелы слева, если бы мы использовали string и итераторы, код бы значительно усложнился
	while(true){
		size_t space = str.find(' ');
		result.push_back(str.substr(0, space));

		if(space == s.npos)
			break;
		else
			s.remove_prefix(space + 1);
	}
	return result;	
}

	string str{ R"( one two three)" };
	const char* cstr{ R"(three two one)" };
	SplitIntoWord(str);
	SplitIntoWord(cstr);
```

Т.к. string_view это указатель на начало строки и его длина, то не стоит в функции принимать значение по ссылке, если в дальнейшем строка уничтожится, string_view будет не понятно на что указывать, лучше не использовать `const string&`, а `string_view` и принимать по значению.


У `string_view` есть конструктор принимающий const char* и длину

```cpp
const char* raw { "One two three" };
size_t length { 3 };
SplitIntoWords({raw, length});//string_view переданный в SplitIntoWords будет иметь значение One
```

Мы не можем неявно создать `string` из `string_view`

```cpp
void handleExtension(const string& extension) { /* ... */ }

...

handleExtension(SplitIntoWord(str));//Error
handleExtension(string{ SplitIntoWord(str) }); //OK
handleExtension(SplitIntoWord(str).data()); //OK
```

Если мы используем `string_view` или `const string&` как член класса, нужно проследить, чтобы данные на которые она указывает были валидны на протяжении всей жизни объекта.

## string_view and temporary string
Поскольку `string_view` это указатель на объект, инициализирование его временные значением повлечет за собой UB, т.к. переместившись на следующую строчку временная строка удаляется и непонятно на что указывается `string_view`.

```cpp
string s { "Hello" };
string_view sv { s + " World!" };
cout << sv;
```

## string_view literals
Чтобы интерпретировать литерал как `string_view` нужно добавить `sv`

```cpp
auto sv { "My string"sv }
```


Локализация и wide character - Марк Грегоир C++ professional 21 глава