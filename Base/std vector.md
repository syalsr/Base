# std::vector
Вектор имеет [[Категории итераторов#Random access iterators]]
```cpp
template <class T, class Allocator = allocator<T>> 
class vector{}
vector<int> intVector(10, 100); // Creates vector of 10 ints with value 100
vector<string> stringVector(10, "hello");

vector<int> intVector(10);
...
intVector.assign(5, 100);//переиспользуем ненужный вектор, теперь в нем хранятся 5 значений
intVector.assign({ 1, 2, 3, 4 });

vectorOne.swap(vectorTwo);

vector vec { 1, 2, 3 };
int* data1 { vec.data() };//получаем указатель на первый элемент
int* data2 { data(vec) };
```

## insert
Чтобы вставить элемент нужен итератор на ту позицию куда вставлять. Какие элементы можно вставлять:
1. одиночный
2. диапазон контейнера с помощью передачи итераторов
3. initializer_list
4. количество элементов для вставки и сам элемент

## erase
Что нужно чтобы удалить элемент(ы):
1. позицию - удаление 1 элемента
2. диапазон итераторов - удаление набора элементов

## emplace_back vs push_back
Поскольку emplace_back создает объект на месте, то нет смысла передавать туда локальный объект, т.к. произойдет копирование, нужно передавать параметры конструктора.

```cpp
struct Point
{
    int x, y;
    Point(int x_, int y_) : x(x_), y(y_)
    {}
    Point(const Point&)
    {
        cout << "& \n";
    }
    Point(const Point&&)
    {
        cout << "&& \n";
    }
};
int main()
{
    vector<Point> vec;
    vector<Point>vec1;
    vec.push_back(Point(10, 20));//ctor + move ctor
    vec1.emplace_back(10, 29);//ctor
    vec1.emplace_back(Point(12, 31));//ctor + move ctor + move ctor
}
```

## Reference wrapper in vector
```cpp
string str1 { "Hello" };
string str2 { "World" };

vector<reference_wrapper<string>> vec{ ref(str1) };//создаем вектор ссылок на стринг
vec.push_back(ref(str2));

vec[1].get() += "!";
cout << str1 << " " << str2 << endl;
```

## Capacity
```cpp
vector<int> v = {1,2,3};
```

При создание вектора из 3 чисел на стеке будет указатель на кучу, длина и capacity этого вектора. Capacity - доступное количество памяти. При реалокации capacity увеличивается в 1.5 раза, старые итераторы и ссылки инвалидируются

![[../Files/Pasted image 20220216133815.png]]

Сейчас вектор полностью заполнен, если мы хотим добавить новое число, его capacity нужно увеличить, увеличится оно в 2 раза. То есть выделяет новый блок памяти из 6 int, копирует старые значения в новые ячейки.

![[../Files/Pasted image 20220216134117.png]]

Какой вывод можно сделать?

Если вы заранее знаете размер вектора, то лучше его указать, чтобы не было ненужных копирований элементов

Сейчас в векторе после элемента 4 хранятся мусорные значения(напомню capacity=6), если мы хотим очистить память от этих двух чисел, то можем использовать:

```cpp
v.shrink_to_fit();
```

## Инвалидация итераторов и ссылок в векторе
```cpp
vector<int> v = {1,2,3};
int& first = v[0];
auto it = find(v.begin(), v.end(), 3);
v.push_back(4);
cout << first; //теперь first не указывает на первый элемент вектора, поскольку после push_back выделился новый блок памяти и туда скопировались все элементы. Ссылка перестала быть валидной - инвалидировалась, как и итератор
```

Если вы заботитесь о перфомансе программы, то стоит заранее задавать размер вектора, чтобы не было лишних аллокаций и инвалидаций.

```cpp
vector<int> v(100);
v[0] = 0;
int& ref = v[0];
cout << ref << "\n";
cout << v.size() << " " << v.capacity() << "\n";
v.push_back(101);
cout << ref << "\n";
cout << v.size() << " " << v.capacity() << "\n";
//0
//100 100
//-572662307
//101 150
```

При reserve также происходит инвалидация, увеличивается capacity, size остается тем же.

## Инициализация вектора
```cpp
// это вектор [14, 14, 14] 
vector v1 (3, 14); 
// а это вектор [3, 14] 
vector v2 {3, 14};//вызывается конструктор с initializer_list
```