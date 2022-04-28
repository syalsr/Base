ооооо# Templates
# Зачем нужны шаблоны
Если у нас есть несколько функций с одинаковым поведением, но разными типами параметров, либо несколько классов с одинаковым набором методов и данных, но с разными типами этих методов и данных, то можно сделать шаблон функции/шаблон класса, чтобы уменьшить повторяющийся код

[[Шаблоны функций]]
[[Шаблоны классов]]

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

# Инициализация объектов по умолчанию
```cpp
//befor C++11
template<typename T>
class X
{
	T x();
	X() : x()
	{}
	void test(T x = T());
}

//since C++11
template<typename T>
class X
{
	T x{};
	X() : x{}
	{}
	void test(T x = T{});
}
```

# Обобщенные лямбда выражения и шаблоны членов
Такие сокращенные лямбда выражения

```cpp
[] (auto x, auto y) {
	return x + y;
}
```

Трансформируются в это

```cpp
class SomeCompilerSpecificName {
public:
	SomeCompilerSpecificName(); // constructor only callable by compiler
	
	template<typename T1, typename T2>
	auto operator() (T1 x, T2 y) const {
		return x + y;
	}
};
```

# Шаблоны переменных
С C++14 можно параметризовывать переменные определенным типом - это называется шаблон переменной. Определение шаблона должна находиться вне функций и блоков.

```cpp
template<typename T>  
constexpr T pi{3.1415926535897932385};  
  
int main()  
{  
    cout << pi<double> << endl;//3.14159  
    cout << pi<float> << endl;//3.14159  
    //cout << pi<int> << endl;//error, double не может сузиться в int
}
```

Шаблон переменной можно иметь тип по умолчанию

```cpp
template<typename T = long double>
constexpr T pi = T{3.1415926535897932385};

int main()
{
	std::cout << pi<> << '\n'; // outputs a long double
	std::cout << pi<float> << '\n'; // outputs a float
}
```

Шаблоны переменных могут быть параметризованы параметрами не являющимися типами.

```cpp
template<int N>  
std::array<int,N> arr{}; // array with N elements, zero-initialized  
  
template<auto N>  
constexpr decltype(N) dval = N; // type of dval depends on passed value  
  
int main()  
{  
    std::cout << dval<'c'> << '\n'; // N has value 'c' of type char  
    arr<10>[0] = 42; // sets first element of global arr  
    for (std::size_t i=0; i<arr<10>.size(); ++i) { // uses values set in arr  
        std::cout << arr<10>[i] << '\n';  
    }  
}  
/*  
c  
42  
0  
0  
0  
0  
0  
0  
0  
0  
0  
*/
```

# Шаблоны данных
```cpp
template<typename T>
class MyClass {
public:
	static constexpr int max = 1000;
};

template<typename T>
int myMax = MyClass<T>::max;

int main()
{
	auto i = myMax<std::string>;//можем написать это
	//вместо этого auto i = MyClass<std::string>::max;
}
```

```cpp
namespace std {  
    template<typename T> class numeric_limits {  
    public:  
        ...  
        static constexpr bool is_signed = false;  
        ...  
    };  
}  
  
template<typename T>  
constexpr bool isSigned = std::numeric_limits<T>::is_signed;  
  
int main()  
{  
    cout << isSigned<char>;//можем написать это
    //вместо этого std::numeric_limits<char>::is_signed
}
```

# Семантика перемещения и enable_if
Нужно уменьшить этот код используя шаблоны
```cpp
class X {  
    ...  
};  
void g (X&) {  
    std::cout << "g() for variable\n";  
}  
  
void g (X const&) {  
    std::cout << "g() for constant\n";  
}  
void g (X&&) {  
    std::cout << "g() for movable object\n";  
}  
// let f() forward argument val to g():  
void f (X& val) {  
    g(val); // val is non-const lvalue => calls g(X&)  
}  
void f (X const& val) {  
    g(val); // val is const lvalue => calls g(X const&)  
}  
void f (X&& val) {  
    g(std::move(val)); // val is non-const lvalue => needs std::move() to call g(X&&)  
}  
int main()  
{  
    X v; // create variable  
    X const c; // create constant  
    f(v); // f() for nonconstant object calls f(X&) => calls g(X&)  
    f(c); // f() for constant object calls f(X const&) => calls g(X const&)  
    f(X()); // f() for temporary calls f(X&&) => calls g(X&&)  
    f(std::move(v)); // f() for movable variable calls f(X&&) => calls g(X&&)  
}
```

Если сделать так:

```cpp
template<typename T>
void f (T val) {
	g(T);
}
```

То код не будет работать для перемещения, поскольку именнованная && это lvalue C++11 предоставляет perfect forwarding - прямую передачу параметров.

```cpp
class X {  
    ...  
};  
void g (X&) {  
    std::cout << "g() for variable\n";  
}  
void g (X const&) {  
    std::cout << "g() for constant\n";  
}  
void g (X&&) {  
    std::cout << "g() for movable object\n";  
}  
// let f() perfect forward argument val to g():  
template<typename T>  
void f (T&& val) {  
    g(std::forward<T>(val)); //ведет себя по разному, в отличии от передаваемого значения 
}  
int main()  
{  
    X v; // create variable  
    X const c; // create constant  
    f(v); // f() for variable calls f(X&) => calls g(X&)  
    f(c); // f() for constant calls f(X const&) => calls g(X const&)  
    f(X()); // f() for temporary calls f(X&&) => calls g(X&&)  
    f(std::move(v)); // f() for move-enabled variable calls f(X&&) => calls g(X&&)  
}
```

## Шаблоны специальных методов
```cpp
class Person  
{  
private:  
    std::string name;  
public:  
// constructor for passed initial name:  
    explicit Person(std::string const& n) : name(n) {  
        std::cout << "copying string-CONSTR for '" << name << "'\n";  
    }  
    explicit Person(std::string&& n) : name(std::move(n)) {  
        std::cout << "moving string-CONSTR for '" << name << "'\n";  
    }  
// copy and move constructor:  
    Person (Person const& p) : name(p.name) {  
        std::cout << "COPY-CONSTR Person '" << name << "'\n";  
    }  
    Person (Person&& p) : name(std::move(p.name)) {  
        std::cout << "MOVE-CONSTR Person '" << name << "'\n";  
    }  
};  
int main()  
{  
    std::string s = "sname";  
    Person p1(s); // init with string object => calls copying string-CONSTR  
    Person p2("tmp"); // init with string literal => calls moving string-CONSTR  
    Person p3(p1); // copy Person => calls COPY-CONSTR  
    Person p4(std::move(p1)); // move Person => calls MOVE-CONST  
}
```

Используя perfect forwarding может из 2 конструкторов сделать 1

```cpp
class Person  
{  
private:  
    std::string name;  
public:  
// generic constructor for passed initial name:  
    template<typename STR>  
    explicit Person(STR&& n) : name(std::forward<STR>(n)) {  
        std::cout << "TMPL-CONSTR for '" << name << "'\n";  
    }  
// copy and move constructor:  
    Person (Person const& p) : name(p.name) {  
        std::cout << "COPY-CONSTR Person '" << name << "'\n";  
    }  
    Person (Person&& p) : name(std::move(p.name)) {  
        std::cout << "MOVE-CONSTR Person '" << name << "'\n";  
    }  
};

int main()
{
	std::string s = "sname";
	Person p1(s); // init with string object => calls TMPL-CONSTR
	Person p2("tmp"); // init with string literal => calls TMPL-CONSTR
}
```

```cpp
Person p3(p1); // ERROR
Person p4(std::move(p1)); // OK: move Person => calls MOVE-CONST
Person const p2c("ctmp"); // init constant object with string literal
Person p3c(p2c); // OK: copy constant Person => calls COPY-CONSTR
```

Копирование non const объекта не удалось, потому что в данном случае вызывается

```cpp
explicit Person(STR&& n) : name(std::forward<STR>(n)) {  
    std::cout << "TMPL-CONSTR for '" << name << "'\n";  
}
```

А не предполагаемый конструктор копирования, STR заменяется типом `Person&`

Используем enable_if для решения

```cpp
template<typename STR,
typename = std::enable_if_t<std::is_convertible_v<STR, std::string>>>//если параметр шаблон может быть преобразован в string
Person(STR&& n);

//если да, то раскрывается до
template<typename STR, typename = void>
Person(STR&& n);

//если нет весь шаблон функции игнорируется
template<typename T>
using EnableIfString = std::enable_if_t<std::is_convertible_v<T, std::string>>;
template<typename STR, typename = EnableIfString<STR>>
Person(STR&& n);
```

В результате:

```cpp
template<typename T>  
using EnableIfString = std::enable_if_t<std::is_convertible_v<T,std::string>>;  
class Person  
{  
private:  
    std::string name;  
public:  
// generic constructor for passed initial name:  
    template<typename STR, typename = EnableIfString<STR>>  
    explicit Person(STR&& n)  
            : name(std::forward<STR>(n)) {  
        std::cout << "TMPL-CONSTR for '" << name << "'\n";  
    }  
    // copy and move constructor:  
    Person (Person const& p) : name(p.name) {  
        std::cout << "COPY-CONSTR Person '" << name << "'\n";  
    }  
    Person (Person&& p) : name(std::move(p.name)) {  
        std::cout << "MOVE-CONSTR Person '" << name << "'\n";  
    }  
};

int main()
{
	std::string s = "sname";
	Person p1(s); // init with string object => calls TMPL-CONSTR
	Person p2("tmp"); // init with string literal => calls TMPL-CONSTR
	Person p3(p1); // OK => calls COPY-CONSTR
	Person p4(std::move(p1)); // OK => calls MOVE-CONST
}
```

Вместо is_convertible(типы неявно преобразуются) можно использовать is_constructible, в этом случае порядок аргументов противоположный.

#do/start почитать про их разницу и применения.

## enable_if
Позволяет игнорировать шаблоны при определенных компайл тайм условиях.

```cpp
template<typename T>
typename std::enable_if<(sizeof(T) > 4)>::type
foo() {}

template<typename T>
std::enable_if_t<(sizeof(T) > 4)>
foo() {}
```

Это определение foo<>() игнорируется, если условие `sizeof(T)>4` дает false, если `true`, то шаблон раскрывается до 

```cpp
void foo() {}
```

1. Если выражение вычисляется как true, член type дает тип
	1. void, если второй аргумент не передан
	2. иначе второй аргумент шаблона
2. Если выражение вычисляется как false, член type не определен. Благодаря SFINAE, шаблон функции с выражением `enable_if` в этом случае игнорируется.

```cpp
//второй вариант написания
template<typename T, typename = std::enable_if_t<(sizeof(T) > 4)>>
void foo() {}

//третий вариант написания
template<typename T>
using EnableIfSizeGreater4 = std::enable_if_t<(sizeof(T) > 4)>;
template<typename T, typename = EnableIfSizeGreater4<T>>
void foo() {}
```

## Отключение специальных методов
Мы не можем использовать enable_if для стандартных копирующих и перемещающих конструкторов и операторов присваивания, потому что шаблоны методов никогда не учитываются как специальные методы и игнорируются, когда, например требуется копирующий конструктор.

```cpp
class C {  
public:  
    C(){}  
    template<typename T>  
    C (T const&) {  
        std::cout << "tmpl copy constructor\n";  
    }  
};  
  
int main()  
{  
    C c{};  
    C f{c};//вызывается нешаблонный созданный компилятором конструктор копирования  
}
```

Если удалим конструктор копирования для объектом const volatile, все будет хорошо

```cpp
class C {  
public:  
    C(){}
	C(C const volatile&) = delete;
    template<typename T>  
    C (T const&) {  
        std::cout << "tmpl copy constructor\n";  
    }  
};  
  
int main()  
{  
    C c{};  
    C f{c};//используется шаблон конструктора
}
```

# SFINAE - Substitution Failure Is Not An Error 
Провал подстановки не является ошибкой. Если в результате подстановки в непосредственном контексте класса (функции, алиаса, переменной) возникает невалидная конструкция, эта подстановка неуспешна, но не ошибочна.

Подстановка должна быть провалена в объявлении:

```cpp
int negate (int i) { return -i; } 
template T negate(const T& t) { 
	typename T::value_type n = -t(); //в перегрузке выигрывает эта функция, подстановка состоялась, но у double нет вложенного типа value_type, подстановка состоялась, это не SFINAE 
} 
negate(2.0); // ошибка второй фазы
```

Допустим у нас есть 2 типа и мы хотим знать равны ли они:

```cpp
template int foo() { 
	// как вернуть 1 если T == U и 0 если нет? 
}
```

Это задача отображения из типов на числа - называется интегральной константой:

```cpp
template <typename T, T v> struct integral_constant {  
    static const T value = v;  
    typedef T value_type;  
    typedef integral_constant type;  
    operator value_type() const { return value; }  //оператор приведения типов
};

using ic6 = integral_constant<int, 6>; 
auto n = 7 * ic6{};
```

Зная это решим задачу:

```cpp
using true_type = integral_constant<bool, true>; //из std
using false_type = integral_constant<bool, false>;//из std

namespace s  
{  
    template<typename T, typename U>  
    struct is_same : std::false_type //публичное наследование  
    {};  
  
    template<typename T>  
    struct is_same<T, T> : std::true_type //публичное наследование  
    {}; // для T == T  
  
    template<typename T, typename U>  
    using is_same_t = typename is_same<T, U>::type;  
  
}  
  
int main() {  
    std::cout << std::boolalpha;  
    std::cout << s::is_same_t<int, int>{} << std::endl;  
    std::cout << s::is_same_t<int, char>{} << std::endl;  
}
```

# is reference remove reference
```cpp
Проверяет, есть ли ссылка
template <typename T> struct is_reference : false_type {};  
template <typename T> struct is_reference<T&> : true_type {};  
template <typename T> struct is_reference<T&&> : true_type {};  

Убирает ссылку если есть
template <typename T> struct remove_reference { using type = T; };  
template <typename T> struct remove_reference<T&> { using type = T; };  
template <typename T> struct remove_reference<T&&> { using type = T; };

template<typename T> 
using remove_reference_t = typename remove_reference<T>::type;
```

# Категории типов
Любой тип в языке C++ попадает под одну из следующих категорий:

```cpp
is_void  
is_null_pointer//именно тип nullptr_t  
is_integral, is_floating_point // для T и для cv T& транзитивно  
is_array; // только встроенные, не std::array  
is_pointer; // включая указатели на обычные функции  
is_lvalue_reference, is_rvalue_reference  
is_member_object_pointer, is_member_function_pointer  
is_enum, is_union, is_class  
is_function // обычные функции

std::cout << std::boolalpha << std::is_void::value << '\n';
```

# Свойства типов type traits
```cpp
is_trivially_copyable // побайтово копируемый, memcpy  
is_standard_layout // можно адресовать поля указателем  
is_aggregate // доступна агрегатная инициализация как в C  
is_default_constructible // есть default ctor  
is_copy_constructible, is_copy_assignable  
is_move_constructible, is_nothrow_move_constructible  
is_move_assignable  
is_base_of // B является базой (транзитивно, включая сам тип)  
is_convertible // есть преобразование из A к B
```

Указано не все, проще приложить ссылку https://en.cppreference.com/w/cpp/header/type_traits

# forward
```cpp
template<typename Fun, typename Args>  
decltype(auto) transparent(Fun fun, Args&& args) {  
    return fun(forward<Args>(args));  
}
```

1. Используем decltype(auto), чтобы не срезать тип
2. Args&& используем свертку ссылок
3. forward в зависимости от того какой тип, мувает либо передает ссылку. Если у нас передали lvalue ref то forward не влияет, если передаем rvalue ref или rvalue мувается

```cpp
```cpp
template<typename Fun, typename... Args>  
decltype(auto) transparent(Fun fun, Args&&... args) {  
    return fun(forward<Args>(args)...);  
}
```

# void_t
```cpp
template <typename...> using void_t = void;
```

`void_t<T, U, V>` означает `void` если все типы легальны и нелегален если нелегален хоть один, т.е. это что-то на подобии конъюнкция

Задача: есть две структуры, в одной есть foobar, в другой нет, как определить?

```cpp
struct foo { typedef float foobar; };  
struct bar { };  

std::cout << std::boolalpha << ??? foo << " " << ??? bar;
```

Решение:

```cpp
template <typename, typename = void>  
struct has_typedef_foobar: std::false_type { };  
  
template <typename T>  
struct has_typedef_foobar<T,  
        std::void_t<typename T::foobar>>: std::true_type{};  
  
struct Foo { typedef float foobar; };  
struct Bar {};  
  
int main() {  
    std::cout << std::boolalpha;  
    std::cout << has_typedef_foobar<Foo>{} << std::endl;  
    std::cout << has_typedef_foobar<Bar>{} << std::endl;  
    //true  
    //false
}
```

# declval
Это функция стандартной библиотеки которая имеет только объявления делает из нас rvalue ref

```cpp
template <typename T> struct Tricky {  
    Tricky() = delete;  
    const volatile T foo ();  
};  
int main() {  
    decltype(declval<Tricky<int>>().foo()) t; // int  
}
```

delctype только оценивает тип, поскольку у declval нет тела, все хорошо.

## Конструирование вектора из итераторов
Проблема в том, что передав 2 числа, вызовется конструктор для итераторов, хотя не хотелось бы. 

```cpp
MyVector(size_t nelts, T value);  
template <typename Iter,  
        typename = void_t<decltype(*Iter{}),  
		                  decltype(++Iter{})>//сомнитьтельно, префиксу нужно lvalue  
         >  
MyVector(Iter fst, Iter lst);
```

Вот как это решить:

```cpp
MyVector(size_t nelts, T value);  
template <typename Iter,  
        typename = void_t<decltype(*std::declval<Iter&>()), //вернет l ref 
		                  decltype(++std::declval<Iter&>())>//вернет l ref
        > 				 
MyVector(Iter fst, Iter lst);  
....  
MyVector v1(10, 3); // 1, поскольку 2 провалилось  
MyVector v2(v1.begin(), v1.end()); // 2
```

Эти 2 decltype можно представить как интерфейс void_t, то есть то что он должен уметь, чтобы успешно выполнилась работа конструктора - этот интерфейс называется [[Consepts|концептом]]

# enable_if
Модернизированный void_t.  Хотим чтобы foo было валидно только для input итераторов:

```cpp
template<typename It>
using iterator_category_t = typename std::iterator_traits<IT>::iterator_category;

template<typename It, typename T = std::enable_if_t<std::is_base_of_v<input_iterator_tag,
																	iterator_category_t<It>>>>
void foo(It first, It last)
{}
```

```cpp
template <typename It>  
using iterator_category_t = typename std::iterator_traits<It>::iterator_category;  
  
template <typename It, typename T = std::enable_if_t<  
        std::is_base_of_v<std::random_access_iterator_tag,  
                iterator_category_t<It>>  
>>  
void foo(It first, It last) {  
    std::cout << std::distance(first, last) << std::endl;  
}  
  
int main() {  
    std::vector v{1, 2, 3};  
    foo(v.begin(), v.end());//3  
    std::list l{1, 2, 3};  
    foo(l.begin(), l.end());//error  
    //no matching function for call to    
    // foo(std::__cxx11::list<int, std::allocator<int> >::iterator,    
    //      std::__cxx11::list<int, std::allocator<int> >::iterator)    
    //no type named ‘type’ in ‘struct std::enable_if<false, void>’    
    //using enable_if_t = typename enable_if<_Cond, _Tp>::type;
}
```

# Вывод типов шаблонами
cv-квалификатор - const, volatile, внутренние cv не удаляются
1. Вывод уточненных типов
	1. Ссылки и cv-квалификаторы игнорируются для T, также работает и с auto
2. Вывод уточненных типов
	1. Уточняющая обвязка игнорируется для T, но cv-квалификаторы сохраняются
	2. Типичные уточнители это ссылка и указатель

```cpp
template<typename T> void foo (T t); // неуточнённый 
template<typename T> void bar (T& t); // ссылка 
template<typename T> void buz (T* t); // указатель
```
`

```cpp
template <typename T> void foo(T t);
template <typename T> void bar(T& t);
int x = 42;
int const& y = x;
int const* const z = &x;
foo(x); // → foo<int>(int)
foo(y); // → foo<int>(int)
foo(z); // → foo<int const *>(int const *)
bar(x); // → bar<int>(int&)
bar(y); // → bar<int const>(int const &)
bar(z); // → bar<int const * const>(int const * const &)

template <typename T> void foo(T t);
template <typename T> void buz(T* t);
int x = 42; int* px = &x;
int*& py = px;
int const* const pz = px;
foo(px); // → foo<int*>(int*)
foo(py); // → foo<int*>(int*)
foo(pz); // → foo<int const *>(int const *)
buz(px); // → buz<int>(int*)
buz(py); // → buz<int>(int*)
buz(pz); // → buz<int const>(int const * const)

template <typename T> void foo(T* const& t);
int x = 42;
int const& y = x;
int const* z = &x;
foo(&x); // → foo<int>(int * const &)
foo(&y); // → foo<int const>(int const * const &)
foo(z); // → foo<int const>(int const * const &)
```
