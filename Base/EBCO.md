# Empty Base Class Optimizations
Оптимизации пустого базового класса (EBCO) применяются когда базовый класс хм... пустой 

```cpp
class A{}; 
class B : public A{}; 
A a; assert(sizeof(a) == 1); 
B b; assert(sizeof(b) == 1);
```

Заметьте, класс с хотя бы одним виртуальным методом точно не пустой 

Зачем наследоваться от пустого класса? Чтобы затащить в производный методы(получается, если методы не виртуал, то vtable не создается и класс пустым считается), можем наследоваться от этого класса

```cpp
template <typename T, typename Deleter = default_delete<T>>  
class unique_ptr : private Deleter {//private т.к. нам нужен только интерфейс  
    T *ptr_;  
public:  
    unique_ptr(T *ptr = nullptr, Deleter del = Deleter()) :  
            Deleter(del), ptr_(ptr), del_(del) {}  
    ~unique_ptr() { Deleter::operator()(ptr_); }
```

Если же наш deleter это функция так не получится.

```cpp
enum { SZ = 1000 };  
  
struct CDeleterTy {  
    void operator()(int *t) { delete[] t; }  //если напишем виртуальный деструктор, то класс перестанет быть stateless, что это такое? типа пустой класс? #do/review 
};  
  
auto LDeleter = [](int *t) { delete[] t; };  
using LDeleterTy = decltype(LDeleter);  
  
void FDeleter(int *t) { delete[] t; }  
using FDeleterTy = decltype(&FDeleter);  
  
int main() {  
    int *Uip = new int[SZ]();  
    std::unique_ptr<int, CDeleterTy> Uic{new int[SZ]()};  
    std::unique_ptr<int, LDeleterTy> Uil{new int[SZ](), LDeleter};  
    std::unique_ptr<int, FDeleterTy> Uif{new int[SZ](), FDeleter};  
  
    std::cout << "pi:" << sizeof(Uip) << "\tuic:" << sizeof(Uic);  
    std::cout << "\tuil:" << sizeof(Uil) << "\tuif:" << sizeof(Uif) << std::endl;  
	//pi:8    uic:8   uil:8   uif:16
    delete[] Uip;  
}
```

uic и uil смогли оптимизироваться и не стали равными 16 из-за выравнивания, поскольку лямбда конструируется в класс + она ничего не захватывает.

#do/review 