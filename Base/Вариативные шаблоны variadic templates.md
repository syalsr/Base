# Вариативные шаблоны Variadic templates
Позволяет передавать произвольное количество аргументов произвольного типа.

```cpp
void print()  
{}  
  
template<typename T, typename ... Types>  
void print(T firstarg, Types... args)  
{  
    std::cout << firstarg << " ";  
    print(args...);  
}  
  
int main()  
{  
    int one = 1;  
    double two = 2.5;  
    float three = 3.2f;  
    const char* four = "four";  
    string five = "five";  
    print(one, two, three, four, five);//1 2.5 3.2 four five
    //print расширяется в print<int, double, float, const char*, string>(one, two, three, four, five);
    //далее по рекурсии print<double, float...>(two, three...)
    //и в конце когда выводится последний объект five, firstarg становится пустым и вызывается нешаблонная перегрузка
}
```

Выводим первый аргумент и рекурсивно вызываем print() для остальных аргументов, когда аргументов не осталось вызывается нешаблонная перегрузка, для завершения рекурсии. Без нее программа не скомпилировалась бы.

args - пакет параметров функции.

Если два шаблона функций отличаются только наличием пакета параметров функции, шаблон функции без такого пакета является предпочтительнее.

```cpp
template<typename T>  
void print(T firstarg)  
{  
    cout << firstarg << " ";  
}  
  
template<typename T, typename ... Types>  
void print(T firstarg, Types... args)  
{  
    print(firstarg);  
    print(args...);  
}  
  
int main()  
{  
    int one = 1;  
    double two = 2.5;  
    float three = 3.2f;  
    const char* four = "four";  
    string five = "five";  
    print(one, two, three, four, five);//1 2.5 3.2 four five  
}
```

```cpp
template<typename ... Types> void f(Types ... args);  
template<typename ... Types> void g(Types ... args) {  
    f(args ...); // → f(x, y);  
    f(&args ...); // → f(&x, &y);  
    f(h(args) ...); // → f(h(x), h(y));h(x) вызов функции  
    f(const_cast<const Types*>(&args)...);  
		// → f(const_cast<const int*>(&x), const_cast<const double*>(&y));  
}  
g(1, 1.0); // → g(int x, double y);
```

```cpp
f(h(args...) + h(args)...); // →  f(h(x, y, z) + h(x), h(x, y, z) + h(y), h(x, y, z) + h(z));
f(h(args, args...)...);     // →  f(h(x, x, y, z), h(y, x, y, z), h(z, x, y, z));
```
## Оператор sizeof...
```cpp
template<typename T, typename ... Types>  
void print(T firstarg, Types... args)  
{  
    cout << firstarg << " ";  
    if constexpr(sizeof...(args)>0)//sizeof...(args) - количество оставшихся аргументов  
        print(args...);  
}  
  
int main()  
{  
    int one = 1;  
    double two = 2.5;  
    float three = 3.2f;  
    const char* four = "four";  
    string five = "five";  
    print(one, two, three, four, five);  
  
}  
/*  
1 2.5 3.2 four five  
*/
```

## Fold expressions
Начиная с C++17 можно вычислять результат с применением бинарного оператора ко всем аргументам пакета параметров с необязательным начальным значением.

```cpp
template<typename... T>  
auto foldSum (T... s) {  
    return (... + s); // ((s1 + s2) + s3) ...  
}  
  
int main()  
{  
    cout << foldSum(1,2,3,4,5,6,7,8,9,10);//55  
}
```

| Fold Expression         | Evaluation                                     |
| ----------------------- | ---------------------------------------------- |
| ( ... op pack )         | ((( pack1 op pack2 ) op pack3 ) ... op packN ) |
| ( pack op ... )         | ( pack1 op ( ... ( packN-1 op packN )))        |
| ( init op ... op pack ) | ((( init op pack1 ) op pack2 ) ... op packN )  |
| ( pack op ... op init ) | ( pack1 op ( ... ( packN op init )))           |

Можно использовать почти все бинарные операторы.

```cpp
// define binary tree structure and traverse helpers:  
struct Node {  
    int value;  
    Node* left;  
    Node* right;  
    Node(int i=0) : value(i), left(nullptr), right(nullptr) {  
    }  
};  
  
// traverse tree, using fold expression:  
template<typename T, typename... TP>  
Node* traverse (T np, TP... paths) {  
    return (np ->* ... ->* paths); // np ->* paths1 ->* paths2 ...  
}  
int main()  
{  
    // init binary tree structure:  
    Node* root = new Node{0};  
    root->left = new Node{1};  
    root->left->right = new Node{2};  
      
    auto left = &Node::left;  
    auto right = &Node::right;  
  
    // traverse binary tree:  
    Node* node = traverse(root, left, right);//node.value==2
}
```

```cpp
template<typename... Types>  
void print (Types const&... args)  
{  
    (std::cout << ... << args) << '\n';  
    //1253212312314314132312312312  
    
}  
int main()  
{  
    print(1,2,5,32,123,1,23,14,31,4,1,32,312,312,3,12);  
}
```

В данном случае нет пробелов, нужно создать шаблон класса, который бы добавлял пробелы.

```cpp
template<typename T>  
class AddSpace  
{  
private:  
    T const& ref; // refer to argument passed in constructor  
public:  
    AddSpace(T const& r): ref(r) {  
    }  
    friend std::ostream& operator<< (std::ostream& os, AddSpace<T> s) {  
        return os << s.ref << ' '; // output passed argument and a space  
    }  
};  
  
template<typename... Args>  
void print (Args... args) {  
    ( std::cout << ... << AddSpace<Args>(args) ) << '\n';  
}  
  
template<typename... T>  
void printDoubled (T const&... args)  
{  
    print (args + args...);  
}  
  
template<typename T1, typename... TN>  
constexpr bool isHomogeneous (T1, TN...)  
{  
    return (std::is_same<T1,TN>::value && ...); // since C++17  
}  
int main()  
{  
    print(1,2,5,32,123,1,23,14,31,4,1,32,312,312,3,12);  
    //1 2 5 32 123 1 23 14 31 4 1 32 312 312 3 12  
    printDoubled(1,2,5,32,123,1,23,14,31,4,1,32,312,312,3,12);  
    //2 4 10 64 246 2 46 28 62 8 2 64 624 624 6 24  
    cout << boolalpha  
         << isHomogeneous(1,20)//true - is_same<int, int>::value  
         << isHomogeneous(1, 2, 4.0);//false - is_same<int, int>::value &&
									            //is_same<int, int>::value && 
										        //is_same<int, double>::value  
}
```

```cpp
template<typename C, typename... Idx>  
void printElems (C const& coll, Idx... idx)  
{  
    print (coll[idx]...);  
}  
template<std::size_t... idx, typename C>  
void printElems (C const& coll)  
{  
    print (coll[idx]...);  
}  
int main()  
{  
    std::vector<std::string> coll = {"good", "times", "say", "bye"};  
    printElems(coll,2,0,3);//say good bye  
    printElems<1,1,1,1,1>(coll);//times times times times times  
}
```

К вариативным параметрам шаблона применяются те же правила, что и к обычным параметрам шаблона.

## emplace
Если мы хотим пробросить в функцию какой-то аргумент и пачку параметров, сначала должен идти одиночный аргумент, а потом уже пачка параметров, иначе функция никогда не вызовется.

```cpp
struct StackNode {  
    T elem;  
    StackNode *next;  
    template<typename ... U> StackNode(StackNode *nxt, U ... cargs) :  
            elem (cargs ...), next (nxt) {}

template <typename T> class Stack {  
public:  
    void push(const T& elem) { top_ = new StackNode (top_, elem); }  
    template <typename U> void emplace(U&& ... args) {  
        top_ = new StackNode(top_, forward<U>(args)...);  
    }
```

## Вариативные шаблоны классов
```cpp
template<typename... Elements>
class Tuple;

Tuple<int, std::string, char> t;
```

## Вариативные правила вывода
```cpp
namespace std {
	template<typename T, typename... U> 
	array(T, U...) -> array<enable_if_t<(is_same_v<T, U> && ...), T>, (1 + sizeof...(U))>;
	//если типы элементов не одинаковы, вывод не состоится
}

std::array a{42,45,77};//ok, типы одинаковы
```

## Вариативные базовые классы и using
```cpp
class Customer  
{  
private:  
    std::string name;  
public:  
    Customer(std::string const& n) : name(n) { }  
    std::string getName() const { return name; }  
};  
struct CustomerEq {  
    bool operator() (Customer const& c1, Customer const& c2) const {  
        return c1.getName() == c2.getName();  
    }  
};  
struct CustomerHash {  
    std::size_t operator() (Customer const& c) const {  
        return std::hash<std::string>()(c.getName());  
    }  
};  
// define class that combines operator() for variadic base classes:  
template<typename... Bases>  
struct Overloader : Bases...  
{  
    using Bases::operator()...; // OK since C++17  
};  
int main()  
{  
    // combine hasher and equality for customers in one type:  
    using CustomerOP = Overloader<CustomerHash,CustomerEq>;  
    std::unordered_set<Customer,CustomerHash,CustomerEq> coll1;  
    std::unordered_set<Customer,CustomerOP,CustomerOP> coll2;  
}
```