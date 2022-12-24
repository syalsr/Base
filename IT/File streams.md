# File streams

![](cpp_basic13.png)

При работе с файлами поток `ostream` преобразует объекты, хранящиеся в основной памяти, в потоки байтов и записывает их на диск. Поток `istream` действует наоборот, он считывает поток байтов с диска и составляет из них объект.

![](cpp_basic14.png)

1. `fstream` - автоматически включает в себя `ios_base::in`
2. `ofstream` - автоматически включает в себя `ios_base::out`

Разница между потоками в том, что у конструктор файлов может принимать аргумент имени и режим открытия файла, по умолчанию `ios_base::out`, который начнет записывать данные в файл в начале и перезапишет любые существующие при повторном открытии.

| Constant         | Описание                                                                                   |
| ---------------- | ------------------------------------------------------------------------------------------ |
| ios_base::app    | Открывает и идет до конца файла перед каждой операцией записи                              |
| ios_base::ate    | Открывает и идет до конца файла один раз сразу после открытия                              |
| ios_base::binary | Выполняет ввод/вывод в двоичном режиме, а не в текстовом                                   |
| ios_base::in     | Открывает файл для того чтобы забрать данные, начиная сначала                              |
| ios_base::out    | Открывает файл, чтобы записать данные, начиная сначала и перезаписывая существующие данные |
| ios_base::trunc  | Открывает файл, чтобы записать данные и удаляет все существующие данные                    |

Режимы открытия файлов можно комбинировать:

```cpp
ios_base::out | ios_base::binary | ios_base::trunc
//Хотим открыть файл для вывода в бинарном виде, удаляя существующие данные
```

```cpp
int main(int argc, char* argv[])
{
	ofstream outFile{ "test.txt", ios_base::trunc };
	if (!outFile.good()) {
		cerr << "Error while opening output file!" << endl;
		return -1;
	}
	outFile << "There were " << argc << " arguments to this program." << endl;
	outFile << "They are: " << endl;
	for (int i{ 0 }; i < argc; i++) {
		outFile << argv[i] << endl;
	}
}
```

## Чтение из потока
Создадим файл `.txt` и напишем программу, которая выводит консоль содержание файла:

```
hello world!
second line
```

```cpp
ifstream input("hello.txt");
string line;
getline(input, line);
cout << line << endl;
getline(input, line);
cout << line << endl;
getline(input, line);
cout << line << endl;//в третий раз выведется второе значение тк достигнут конец файла. 
//Тк getline возвращает ссылку на поток, из которого берет данные. Поток можно привести
//к типу bool, причем false будет в случае, когда с потоком уже можно
//дальше не работать

//.............
//если файла hello.txt нет, то код будет работать, ошибок никаких не будет
//желательно чтобы программа должна говорить, что такого файла не существует
//метод is_open() возвращает true если файловый поток открыт и готов работать
if (input.is_open()){
	while (getline(input, line)) {
		cout << line << endl;
	}
	cout << "done!" << endl;
} else {
	cout << "error!" << endl;
}

//....................
//Поток можно приводить к типу bool причем значение true соответствует тому, 
//что с потоком можно работать в данный момент
//Поэтому код можно переписать
ifstream input("helol.txt");
string line;
if (input){
	while (getline(input, line)) {
		cout << line << endl;
	}
	cout << "done!" << endl;
} else {
	cout << "error!" << endl;
}
```

## Разница между текстовым и двоичным режимом
1. В текстовом режиме происходит скрытое преобразование, когда мы что-то туда записывает или считываем оттуда, конец строки заканчивается \n, но у каждой операционной системы свои конечные символы. Windows - \r\n, т.е. при записи в файл в Windows, \n автоматически преобразовывается в \r\n, при чтении из файла \r\n преобразуется в \n
2. В бинарном режиме мы записывает именно те байты, которые просим записать, при чтении байты возвращаются в том же виде, в котором находились в файле.


## Методы входных/выходных потоков
1. `seekx()` - позволяет переместиться в произвольную позицию внутри входного или выходного потока, для входного потока `seekg()`, для выходного `seekp()`, почему их два? Потому что файловый поток и входной и выходной, он хранит данные и текущей позиции для чтения и записи
2. `seekg()` и `seekp()` имеют по 2 перегрузки которые принимают
	1. Один параметр - принимает позицию(тип `std::streampos`) и выполняет поиск по ней
	2. Два параметр - перемещает на относительную позицию в потоке, принимает смещение(тип `std::streamoff`) и позицию(тип `std::streampos`) и выполняет поиск по ней

| Позиция       | Описание                 |
| ------------- | ------------------------ |
| ios_base::beg | Начало потока            |
| ios_base::end | Конец потока             |
| ios_base::cur | Текущая позиция в потоке |

```cpp
outStream.seekp(ios_base::beg);
inStream.seekg(ios_base::beg);

outStream.seekp(2, ios_base::beg);//перемещает на 2 байта вперед относительно начала
inStream.seekg(-3, ios_base::end);//перемещает на 3 байта назад относительно конца
```
3. `tellx()` - узнать текущее местоположение потока, который возвращает `std::streampos`
	1. `teelg()` - входные потока
	2.` tellp()` - выходные потоки

```cpp
streampos curPos { inStream.tellg() };
if (ios_base::beg == curPos) {
	cout << "We're at the beginning." << endl;
}
```

Следующая программа записывает данные в фай и выполняет следующие тесты:

```cpp
int main()
{
	ofstream fout{ "test.out" };
	if (!fout) {
		cerr << "Error opening test.out for writing" << endl;
		return 1;
	}
	// 1. Записываем строку "54321".
	fout << "54321";
	// 2. Проверяем, что находимся в 5 позиции
	streampos curPos{ fout.tellp() };
	if (curPos == 5) {
		cout << "Test passed: Currently at position 5" << endl;
	}
	else {
		cout << "Test failed: Not at position 5" << endl;
	}
	// 3. Сдвигает позицию на 2 относительно начала
	fout.seekp(2, ios_base::beg);
	// 4. Записывает 0 в позицию 2 и закрывает выходной поток
	fout << 0;
	fout.close();
	// 5. Открывает входной поток
	ifstream fin{ "test.out" };
	if (!fin) {
		cerr << "Error opening test.out for reading" << endl;
		return 1;
	}
	// 6. Читает первый токен как int
	int testVal;
	fin >> testVal;
	if (!fin) {
		cerr << "Error reading from file" << endl;
		return 1;
	}
	// 7. Проверяем, что значение 54021.
	const int expected{ 54021 };
	if (testVal == expected) {
		cout << "Test passed: Value is " << expected << endl;
	}
	else {
		cout << "Test failed: Value is not " << expected << "it was" << testVal << endl;
	}
}
```

## Объединение потоков
Между любыми входными и выходными потоками можно создать связь, чтобы обеспечить им одинаковое поведение при доступе. Когда данные запрашиваются из выходного потока, данные из входного автоматически очищаются.

```cpp
ifstream inFile { "input.txt" };
ofstream outFile { "output.txt" };
inFile.tie(&outFile);

outFile << "Hello there!";//мы не использовали endl, поэтому очистки не будет

string nextToken;
inFile >> nextToken;//тут мы уже меняем поток на входной, выходной поток очищается
```

Может соединить выходной поток, с другим выходным потоком 

```cpp
outFile.tie(&anotherOutputFile);
```

## Двунаправленный ввод/вывод
Двунаправленный поток - обеспечивает выполнения ввода, вывода. Для чего нужны? Допустим находить в файле ошибке и сразу их исправлять.

```cpp
bool changeNumberForID(string_view filename, int id, string_view newNumber)
{
	fstream ioData{ filename.data() };
	if (!ioData) {
		cerr << "Error while opening file " << filename << endl;
		return false;
	}
	//Пока не достигнем конца файла
	while (ioData) {
		int idRead;
		ioData >> idRead;
		if (!ioData)
			break;
		if (idRead == id) {
			// Перемещаем позицию записи в текущую позицию чтения
			ioData.seekp(ioData.tellg());
			ioData << " " << newNumber;
			break;
		}
		// Считываем текущий номер для продвижения текущей позиции
		string number;
		ioData >> number;
	}
	return true;
}
int main(int argc, char* argv[])
{
	string namefile = "output.txt";
	string newnumber = "999-999-9999";
	ofstream outFile{ namefile };

	outFile << "123 408-555-0394\n";
	outFile << "124 415-555-3422\n";
	outFile << "263 585-555-3490\n";
	outFile << "100 650-555-3434";

	outFile.close();//если мы не закрое поток, то на момент вызова функции файл output.txt будет пуст
	changeNumberForID(namefile, 124, newnumber);
}
```

## Библиотека поддержки файловой системы std::filesystem
### Путь
Путь может быть абсолютный и относительный

```cpp
path p1 { R"(D:\Foo\Bar)" };
path p2 { "D:/Foo/Bar" };
path p3 { "D:/Foo/Bar/MyFile.txt" };
path p4 { R"(..\SomeFolder)" };
path p5 { "/usr/lib/X11" };

path p { "D:\\Foo" };
p.append("Bar");
p /= "Bar";
cout << p << endl;//D:\\Foo\\Bar\\Bar

path p { "D:\\Foo" };
p.concat("Bar");
p += "Bar";
cout << p << endl;//D:\\FooBarBar

path p { R"(C:\Foo\Bar)" };
for (const auto& component : p) {
	cout << component << endl;
}
/*
	C:
	\\
	Foo
	Bar
*/

path p{ R"(D:\Work\Cpp\Brown\Crach course\output.txt)" };
cout << p.root_name() << endl;
cout << p.filename() << endl;
cout << p.stem() << endl;
cout << p.extension() << endl;
/*
	D:
	output.txt
	output
	.txt
*/
```

Путь представляет собой директорию или файл, который может существовать, а может не существовать. Если мы хотим использовать существующий путь, мы должны построить `directory_entry`

```cpp
path myPath{ R"(D:\Work\Cpp\Brown\Crach course\output.txt)"};
directory_entry dirEntry{ myPath };
if (dirEntry.exists() && dirEntry.is_regular_file()) {
	cout << "File size: " << dirEntry.file_size() << endl;
}
//File size: 70
```

### [[Итераторы]]
Имея путь мы можем сделать итерацию по содержимому папки
```cpp
std::filesystem::directory_iterator start{path}, fin;//итератор для конца сингулярный
std::vector entries(start, fin); // → vector<directory_entry>
//Далее у каждого из entries[i] можно в свою очередь получить path и т. д
```

```cpp
std::vector<directory_entry> contents(directory_entry d);  

auto files_in_subdirs() {  
    std::filesystem::directory_iterator start{"."};  
    std::vector<std::filesystem::directory_entry> res;  
    for (auto &&item : start)  
        if (item.is_directory()) {  
            auto ents = contents(item);  
            res.insert(res.end(),  
                       std::make_move_iterator(ents.begin()),  
                       std::make_move_iterator(ents.end())); // move  
        }  
    return res;  
}
```

Вывести все файлы и их каталоги
```cpp
void printDirectoryStructure(const path& p)
{
	if (!exists(p)) {
		return;
	}
	recursive_directory_iterator begin{ p };
	recursive_directory_iterator end{ };
	for (auto iter{ begin }; iter != end; ++iter) {
		const string spacer(iter.depth() * 2, ' ');
		auto& entry{ *iter }; // Dereference iter to access directory_entry.
		if (is_regular_file(entry)) {
			cout << format("{}File: {} ({} bytes)",
				spacer, entry.path().string(), file_size(entry)) << endl;
		}
		else if (is_directory(entry)) {
			cout << format("{}Dir: {}", spacer, entry.path().string()) << endl;
		}
	}
}

int main(int argc, char* argv[])
{
	path p{ R"(C:)" };
	printDirectoryStructure(p);
}
```