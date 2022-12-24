# Выходные потоки
С помощью оператора `<<` мы можем выводить в поток все встроенные типы данных, для собственных нужно самим переопределять вывод/ввод в/из поток.

```cpp
int i { 7 };
cout << i << endl;//7

char ch { 'a' };
cout << ch << endl;//a

string myString { "Hello World." };
cout << myString << endl;//Hello World.
```

Поскольку `<<` возвращает ссылку на поток, мы можем использовать использовать их для передачи нескольких строк в поток.

```cpp
int j { 11 };
cout << "The value of j is " << j << "!" << endl;//The value of j is 11!
```

## Методы выходных потоков
1. put() - принимает 1 символ
```cpp
cout.put('a');
```
2. write() - принимает массив символов
```cpp
const char* test { "hello there\n" };
cout.write(test, strlen(test));
```
3. flush() - сбрасывает буфер
```cpp
cout << "abc";
cout.flush(); // abc is written to the console.
cout << "def";
cout << endl; // def is written to the console.
```

## Обработки ошибок вывода
Метод good()/bad()проверяет все ли хорошо/плохо с потоком

```cpp
if (cout.good()) {
	cout << "All good" << endl;
}
```

Метод fail() возвращает true, если последняя операция завершилась неудачей

```cpp
cout.flush();
if (cout.fail()) {
	cerr << "Unable to flush to standard out" << endl;
}
```

Потоки имеют оператор преобразования в тип bool, поэтому можно переписать код выше без метода fail()

```cpp
cout.flush();
if (!cout) {
	cerr << "Unable to flush to standard out" << endl;
}
```

good() и fail() возвращают false, если достигнут конец файла. 
```cpp
good() == (!fail() && !eof())
```

## Выходные манипуляторы
Манипуляторы изменяют поведение потока

1. boolalpha и noboolalpha - говорит компиляторы выводит значения bool, как true/false - boolalphs и 0/1 - noboolalpha. По умолчанию стоит noboolalpha 
```cpp
bool myBool{ true };
cout << myBool << endl;
cout << boolalpha << myBool << endl;
cout << noboolalpha << myBool << endl;
/*
1
true
1
*/
```
2. hex, oct, dec - выводит числа в шестнадцатеричном, восьмеричном, десятичном формате.
3. setprecision - устанавливает количество знаков после запятой
```cpp
cout << setprecision(5) << 1.23456789 << endl;
//1.2346
//Либо можно вызвать метод cout.precision(5);
```
4. setw - устанавливает ширину поля для выходных данных, смотри пример с setfill
5. setfill - устанавливает символ, как новый символ заполнения потока, заполняется в соответствии с шириной
```cpp
double dbl{ 1.452 };
cout << setw(7) << setfill('@') << dbl << endl;

int i{ 123 };
printf("%6d\n", i);
cout << setw(6) << i << endl;

printf("%06d\n", i);
cout << setfill('0') << setw(6) << i << endl;

// Заполняем *
cout << setfill('*') << setw(6) << i << endl;
/*
@@1.452
   123
@@@123
000123
000123
***123
*/
```
6. showpoint и noshowpoint - заставляет поток всегда или никогда не показывать десятичную часть для чисел с плавающей точкой без дробной части.
```cpp
double dbl{ 1.452 };
double dbl2{ 5 };
cout << setw(2) << setfill('0') << dbl2 << " " << showpoint << dbl2 << endl;
cout << setw(7) << setfill('@') << dbl << endl;
cout << showpoint << setw(7) << setfill('@') << dbl << endl;
/*
05 5.00000
1.45200
1.45200
*/
```
7. put_money - записывает форматированное денежное значение
```cpp
cout.imbue(locale{ "en_US.UTF-8" });
cout << 1234567 << endl;
//Центы в доллары
cout << put_money("120000") << endl;
/*
1,234,567
1,200.00
*/
```
8. put_time - записывает форматированное временное значение
9. quoted - заключает заданную строку в кавычки и экранирует встроенные кавычки
```cpp
cout << "This should be: \"Quoted string with \\\"embedded quotes\\\".\": "
	 << quoted("Quoted string with \"embedded quotes\".")
	 << endl;
//This should be: "Quoted string with \"embedded quotes\".": "Quoted string with \"embedded quotes\"."
```