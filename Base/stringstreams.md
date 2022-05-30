# stringstreams
`istringstream` и `ostringstream` наследуются от `istream` и `ostream` $=>$ имеют схожее поведение.

```cpp
int main()
{
	cout << "Enter tokens. Control+D (Unix) or Control+Z (Windows) followed by Enter to end." << endl;
	ostringstream outStream;
	string nextToken;
	cout << "Next token: ";
	while (cin >> nextToken) {
		cout << "Next token: ";
		if (nextToken == "done")
			break;
		outStream << nextToken << "\t";
	}
	cout << "The end result is: " << outStream.str();
}
/*
Enter tokens. Control+D (Unix) or Control+Z (Windows) followed by Enter to end.
Next token: yellow
Next token: blue
Next token: white
Next token: brown
Next token: done
Next token: The end result is: yellow   blue    white   brown
*/
```

```cpp
Muffin createMuffin(istringstream& stream)
{
	Muffin muffin;
	// Предполагается, что данные верно отформатированы
	string description;
	int size;
	bool hasChips;
	stream >> description >> size >> boolalpha >> hasChips;
	if (stream) { // запись из потока успешно прошла
		muffin.setSize(size);
		muffin.setDescription(description);
		muffin.setHasChocolateChips(hasChips);
	}
	return muffin;
}
```

Преимущество перед обычной строкой в том, что:
1. У `stringstream` есть текущая позиция
2. Есть манипуляторы и локали
3. Построение большой строки из нескольких маленьких будет производительнее, чем конкатенация обычных строк