# arrays в pure C
Массив - это набор значений одного типа, расположенных в памяти последовательно, нумерация которого начинается с нуля, массив должен иметь размер на этапе компиляции, поэтому во время выполнения мы не сможем добавлять в него элементы, а лишь изменять существующие.

```cpp
const int m = 100;    
int qai[m] ; // Глобальный ма ссив (из 100 чисел типа int) ; "живет вечно "  void f (int n)    
{    
    char lac[20];  // локальный массив; "живет" до выхода из области видимости    
	lac[1] = 'x';    
   *lac = 'c';    // lac[0] = 'c';    
	int num[5] {2, 5}; //{2, 5, 0, 0, 0};    
	int num1[10] {}; //{0, 0, 0, ..., 0};    
	int num2[] {2, 4, 6}; //num2.size = 3(необязательно писать размер, если мы инициализируем массив, компилятор сам считает размер)    
	char plif[] {'h', 'i', 112421031, '\0'}; //ошибка - сужение     
	char ticTacToeBoard[3][3];    
    ticTacToeBoard[1][1] = 'o';    
}
```

Если мы хотим, чтобы количество элементов массива было переменным, то должны разместить его в динамической памяти и обращаться к нему через указатель.

```cpp
void doubleInts(int* theArray, size_t size)
{
	for (size_t i { 0 }; i < size; i++) { theArray[i] *= 2; }
}
// т.к. мы работаем с указателем на массив, то мы изменяем его, а не копию
size_t arrSize { 4 };
int* freeStoreArray { new int[arrSize]{ 1, 5, 3, 4 } };
doubleInts(freeStoreArray, arrSize);
delete [] freeStoreArray;
freeStoreArray = nullptr;

int stackArray[] { 5, 7, 9, 11 };
arrSize = std::size(stackArray); // Since C++17, requires <array>
//arrSize = sizeof(stackArray) / sizeof(stackArray[0]); // Pre-C++17
doubleInts(stackArray, arrSize);//имя массива является указателем на его первый элемент
doubleInts(&stackArray[0], arrSize);
```

```cpp
void doubleInts(int theArray[], size_t size)
{
	for (size_t i { 0 }; i < size; i++) { theArray[i] *= 2; }
}
//хоть здесь уже не указатель, но изменять мы будем оригинал
//передавая массив, все равно передается указатель - это сделано для эффективности
```

Число у массива в квадратных скобках в функциях игнорируется, следующие объявления идентичны:

```cpp
void doubleInts(int* theArray, size_t size);
void doubleInts(int theArray[], size_t size);
void doubleInts(int theArray[2], size_t size);
```

Можно также передавать ссылку на массив, но работает только с массивом на стеке

```cpp
void doubleIntsStack(int (&theArray)[4]);
template<size_t N>
void doubleIntsStack(int (&theArray)[N])//компилятор сам выведет размер массива
{
	for (size_t i { 0 }; i < N; i++) { theArray[i] *= 2; }
}
```

## cdecl - как читать выражения
Общее правило - влево, вправо, вверх, повторить
```cpp
int main() {
  int ai[20] = {0};//массив int'ов
  int *api[20] = {nullptr};//массив указателей
  int (*pai)[20] = &ai;//т.к. скобки нас ограничиывают, то это указатель на массив
  int (&rai)[20] = ai;

  std::cout << api << " + 1 = " << api + 1 << std::endl;//api - это указатель на первый элемент, +1 - перепрыгиваем на второй элемент
  std::cout << pai << " + 1 = " << pai + 1 << std::endl;//pai - это указатель на массив, соответсвенно прибавляя 1 мы перепрыгиваем через весь массив
  rai[2] = 40;
  (*pai)[2] += 2;
  
  std::cout << ai[2] << std::endl;
}
/*
0x7ffcf3b49700 + 1 = 0x7ffcf3b49708
0x7ffcf3b497a0 + 1 = 0x7ffcf3b497f0
42
*/
```