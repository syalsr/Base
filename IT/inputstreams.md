# Ввод с помощью потоков
С помощью оператора `>>` мы можем считывать данные из потока до пробела.

```cpp
string userInput;
cin >> userInput;
cout << "User input was " << userInput << endl;
```

## Обработка ошибок вывода
Большинство ошибок возникают когда нет данных для чтения(конец потока, файла)
```cpp
while (cin) { ... }
while (cin >> ch) { ... }
```

Методы good(), bad(), fail() работают как и на выходных потоках

```cpp
int main()
{
	cout << "Enter numbers on separate lines to add.\n"
		<< "Use Control+D followed by Enter to finish (Control+Z in Windows).\n";
	int sum{ 0 };
	if (!cin.good()) {
		cerr << "Standard input is in a bad state!" << endl;
		return 1;
	}
	while (!cin.bad()) {
		int number;
		cin >> number;
		if (cin.good()) {
			sum += number;
		}
		else if (cin.eof()) {
			break; // Reached end of file
		}
		else if (cin.fail()) {
			// Failure!
			cin.clear(); // Clear the failure state.
			string badToken;
			cin >> badToken; // Consume the bad input.
			cerr << "WARNING: Bad input encountered: " << badToken << endl;
		}
	}
	cout << "The sum is " << sum << endl;
}
```

## Методы входных потоков
1. get() - позволяет вводить данные из потока, упрощенная версия возвращает следующий символ потока
```cpp
string readName(istream& stream)//Можем передать в поток имя с пробелами
{
	string name;
	while (stream) { // Or: while (!stream.fail()) {
		int next{ stream.get() };
		if (!stream || next == std::char_traits<char>::eof())
			break;
		name += static_cast<char>(next);// Append character.
	}
	return name;
}
```

Передаем поток по non-const ссылки, его позиции меняется, к тому же мы не можем использовать const ссылку. Для сохранения результата метода get() используем int поскольку он может вернуть конец потока, что не является символьный представлением `std::char_traits<char>::eof()`

Более упрощенный вариант:

```cpp
string readName(istream& stream)
{
	string name;
	char next;
	while (stream.get(next)) {
		name += next;
	}
	return name;
}
```
2. unget() - предыдущий символ возвращается в поток, если текущая позиция находится в начале, метод завершится неудачно.
```cpp
void getReservationData()//Бронируем на такое то имя такой-то номер отеля
{
	string guestName;
	int partySize{ 0 };
	// Read characters until we find a digit
	char ch;
	cin >> noskipws;
	while (cin >> ch) {
		if (isdigit(ch)) {
			cin.unget();
			if (cin.fail())
				cout << "unget() failed" << endl;
			break;
		}
		guestName += ch;
	}
	// Read partySize, if the stream is not in error state
	if (cin)
		cin >> partySize;
	if (!cin) {
		cerr << "Error getting party size." << endl;
		return;
	}
	cout << "Thank you " <<guestName << " party of " << partySize << endl;
	if (partySize > 10) {
		cout << "An extra gratuity will apply." << endl;
	}
}
```
3. putback() - помещает нужный символ  символ в поток
```cpp
int main()
{
	char c;
	cin >> c;
	cout << "Retrieved " << c << " before putback('c')." << endl;
	cin.putback('c'); // 'e' will be the next character read off the stream.
	cin >> c;
	cout << "Retrieved " << c << " after putback('c')." << endl;
	cin >> c;
	cout << c << endl;
	cin >> c;
	cout << c;
}
/*
abcdefghji
Retrieved a before putback('c').
Retrieved c after putback('c').
b
c
*/
```
4. peek() - позволяет предварительно посмотреть на значение следующего символа без смещения текущей позиции
```cpp
void getReservationData()
{
	string guestName;
	int partySize{ 0 };
	// Read characters until we find a digit
	cin >> noskipws;
	while (true) {
		// 'peek' at next character
		char ch{ static_cast<char>(cin.peek()) };
		if (!cin)
			break;
		if (isdigit(ch)) {
			// next character will be a digit, so stop the loop
			break;
		}
		// next character will be a non-digit, so read it
		cin >> ch;
		if (!cin)
			break;
		guestName += ch;
	}
	// Read partySize, if the stream is not in error state
	if (cin)
		cin >> partySize;
	if (!cin) {
		cerr << "Error getting party size." << endl;
		return;
	}
	cout << format("Thank you '{}', party of {}",
		guestName, partySize) << endl;
	if (partySize > 10) {
		cout << "An extra gratuity will apply." << endl;
	}
}
```
5. getline() - считывает последовательность до конца строки, т.е. по умолчанию до \n, перевода строки $=>$ можно класть строки содержащие пробелы, можно задать свой разделитель
```cpp
char buffer[BufferSize] { 0 };
cin.getline(buffer, BufferSize);

string myString;
getline(cin, myString);

cout << "Enter multiple lines of text. Use an @ character to signal the end of the text.\n> ";
string myString;
getline(cin, myString, '@');
cout << "Read text: \"" << myString << "\"" << endl;
/*
> first line
second line
third line @
Read text: "first line
second line
third line "
*/
```

## Входные манипуляторы
1. boolalpha и noboolalpha
2. dec, hex, and oct
3. skipws и noskipws - говорим потоку пропускать или нет пробел, по умолчанию пропускать
4. ws
5. get_money
6. get_time
7. quoted - считывает строку заключенную в кавычки в которой есть внутренние экранированные кавычки