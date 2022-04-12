# Sequential containers
Контейнеры называются так, поскольку их элементы хранятся последовательно.
# Vector
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

## vector bool
Поскольку bool хранит 2 значения на выбор true, false, для оптимизации памяти, `vector<bool` представляет собой массив 0 и 1, каждый элемент занимает только 1 бит

#do/review дописать что-то про vector bool из других источников, в книге я слабо понял это все

# Array
Это вектор, но память выделяется на стеке, поэтому про объявлении мы должны указать тип и размер, обертка над [[C style arrays|сишным массивом]]. Поддерживает [[Категории итераторов#Random access iterators]]
```cpp
int main()
{
    array<int, 3> arr{ 9, 8, 7 };
    cout << "Array size = " << arr.size() << endl;
    for (const auto& i : arr) {
        cout << i << endl;
    }
    cout << arr[0] << " " << get<0>(arr) << "\n";
    cout << "Performing arr.fill(3)..." << endl;
    arr.fill(3);
    for (auto iter{ cbegin(arr) }; iter != cend(arr); ++iter) {
        cout << *iter << endl;
    }
}
/*
    Array size = 3
    9
    8
    7
    9 9
    Performing arr.fill(3)...
    3
    3
    3
*/
```

# Deque
Двусторонняя очередь. Новые приходят в конец и удаляются из начала(или наоборот).

```cpp
d.push_back (x)    // добавление в конец 
d.pop_back (x)     // удаление из конца 
d.push_front (x)   // добавление в начало 
d.pop_front (x)    // удаление из начала 
d.emplace_front(x) // создает новый элемент в начале
d[i] // обращение к элементу по индексу
```
Зачем использовать? Если нужно работать с двумя концами.

В отличии от вектора элементы хранятся не по порядку, а блоками, следовательно инвалидации ссылок можно избежать.

![[../Files/Pasted image 20220216140656.png]]

В деке хранится вектор указателей на чанки(блоки), количество элементов и сдвиг.

![[../Files/Pasted image 20220216141001.png]]

Как тогда найти второй элемент? К сдвигу прибавляем индекс, т.е. нам нужен 4 элемент, размер блока 3, $4/3=1$, значит 1 чанк и там идем в элемент 4%3, то есть берем остаток от деления и получаем, что в первом блоке памяти нам нужен первый элемент.

В итоге, нет инвалидации ссылок, просто вставлять в начало и конец, но сложно интерироваться и получать элемент по индексу.

# List
Для каждого элемента свой блок памяти в котором хранятся значение и адрес следующего и предыдущего элементов.

![[../Files/Pasted image 20220216143504.png]]

С такой реализацией можно быстро удалять элементы из любой позиции, все указатели и итераторы останутся валидными, если элемент на который они указывали, не удалили.

Поскольку блоки не расположены как у массива, для доступа к элементам вместо `[]` используются [[Категории итераторов#Bidirectional iterators]]

Итог: лист эффективнее вектора в удалении, допустим мы удаляем элементы в цикле, чем меньше элементов за цикл удаляется тем list эффективнее, поскольку блоки не идут последовательно в памяти, а прыгать с блока на блок на эффективно

```cpp
int main()
{
    list<int> leaf;
    leaf.push_back(1);
    leaf.push_front(2);
    leaf.pop_back();
    leaf.pop_front();
    leaf.emplace_back(1);
    leaf.emplace(leaf.begin(), 3);
    leaf.emplace_front(4);
    leaf.clear();
}
```

## Splicing
Перемещает элементы из одного list в другой, list из которого перемещаются элементы становится пустым
```cpp
int main()
{
    list<string> dictionary{ "aardvark", "ambulance" };
    list<string> bWords{ "bathos", "balderdash" };
    dictionary.push_back("canticle");
    dictionary.push_back("consumerism");
    // Splice the b words into the main dictionary.
    if (!bWords.empty()) {
        // Get an iterator to the last b word.
        auto iterLastB{ --(cend(bWords)) };
        // Iterate up to the spot where we want to insert b words.
        auto it{ cbegin(dictionary) };
        for (; it != cend(dictionary); ++it) {
            if (*it > *iterLastB)
                break;
        }
        // Add in the b words.This action removes the elements from bWords.
            dictionary.splice(it, bWords);
    }
    // Print out the dictionary.
    for (const auto& word : dictionary) {
        cout << word << endl;
    }
}
```


# Forward list
Для каждого элемента свой блок памяти в котором хранятся значение и адрес следующего элемента. Поддерживает [[Категории итераторов#Forward iterators]], т.е. может ходить только в одном направлении

![[../Files/Pasted image 20220216143448.png]]

```cpp
int main()
{
    forward_list<int> list1{ 5, 6 };
    forward_list list2{ 1, 2, 3, 4 };
    forward_list list3{ 7, 8, 9 };
    
    list1.splice_after(list1.before_begin(), list2);
    list1.push_front(0);

    auto iter{ list1.before_begin() };//из-за того что нет операции -- не можем сделать prev(list1.end())
    auto iterTemp{ iter };
    while (++iterTemp != end(list1)) { ++iter; }
    list1.insert_after(iter, cbegin(list3), cend(list3));
    
    for (auto& i : list1) { cout << i << ' '; }
}
```

Разница между list и forward list(1 - контейнер поддерживает операцию, 0 - контейнер не поддерживает операцию)


| OPERATION                               | list | forward_list |
| --------------------------------------- | ---- | ------------ |
| assign()                                | 1    | 1            |
| back()                                  | 1    | 0            |
| before_begin()                          | 0    | 1            |
| begin()                                 | 1    | 1            |
| cbefore_begin()                         | 0    | 1            |
| cbegin()                                | 1    | 1            |
| cend()                                  | 1    | 1            |
| clear()                                 | 1    | 1            |
| crbegin()                               | 1    | 0            |
| crend()                                 | 1    | 0            |
| emplace()                               | 1    | 0            |
| emplace_after()                         | 0    | 1            |
| emplace_back()                          | 1    | 0            |
| emplace_front()                         | 1    | 1            |
| empty()                                 | 1    | 1            |
| end()                                   | 1    | 1            |
| erase()                                 | 1    | 0            |
| erase_after()                           | 0    | 1            |
| front()                                 | 1    | 1            |
| insert()                                | 1    | 0            |
| insert_after()                          | 0    | 1            |
| iterator / const_iterator               | 1    | 1            |
| max_size()                              | 1    | 1            |
| merge()                                 | 1    | 1            |
| pop_back()                              | 1    | 0            |
| pop_front()                             | 1    | 1            |
| push_back()                             | 1    | 0            |
| push_front()                            | 1    | 1            |
| rbegin()                                | 1    | 0            |
| remove()                                | 1    | 1            |
| remove_if()                             | 1    | 1            |
| rend()                                  | 1    | 0            |
| resize()                                | 1    | 1            |
| reverse()                               | 1    | 1            |
| reverse_iterator/const_reverse_iterator | 1    | 0            |
| size()                                  | 1    | 0            |
| sort()                                  | 1    | 1            |
| splice()                                | 1    | 0            |
| splice_after()                          | 0    | 1            |
| swap()                                  | 1    | 1            |
| unique()                                | 1    | 1            |