# Наследование
* [[Введение в наследование]]
* [[Спецификаторы наследования]]
* [[20 Paradigms#Отношения между классами]]
* [[final]]
* [[Переопределение методов]]
* [[Множественное наследование]]
* [[Проблемы возникающие во время наследования]]
* [[Передача в функцию полиморфных объектов по ссылке или указателю]]
* [[Перегрузка методов]]
* [[Чем хороша виртуальность]]
* [[Чисто виртуальные методы и абстрактный класс]]
* [[Особые случаи при переопределения методов]]
* [[casting C++#dynamic_cast]]
* [[Таблица виртуальных функций]]

 Проблема срезки
```cpp
struct A {  
    int a_;  
    A(int a) : a_(a) {}  
};  
  
struct B : public A {  
    int b_;  
    B(int b) : A(b / 2), b_(b) {}  
};  
  
std::ostream &operator<<(std::ostream &os, const B &b) {  
    os << b.a_ << " " << b.b_;  
    return os;  
}  
  
int main() {  
    B b1(10); // a_ = 5, b_ = 10  
    B b2(8);  // a_ = 4, b_ = 8  
    A &a_ref = b2;  
    a_ref = b1; // a_ = 5, b_ = 8  
    std::cout << b2 << std::endl;  
}
```

`a_ref = b1` - вызывается созданный компилятор оператор присваивания класса A, потому что оператор не ведет себя полиморфно.

Полиморфная функция - та функция которая ведет себя по разному при различных типах - перегрузки, шаблоны, виртуальные, т.е. та функция у которой есть хотя бы один полиморфный параметр, а полиморфный параметр - параметр который может быть разного типа.

Статический тип - тип известный на этапе компиляции. 
Динамический тип - тип который получается в результате выполнения

```plantuml
class ISquare
class Triangle
class Polygon

ISquare <|-- Triangle
ISquare <|-- Polygon
```




```cpp
double sum_square(const ISquare &lhs,  
                  const ISquare &rhs) {  
    return lhs.square() + rhs.square();  
}  
Triangle t; Polygon p;  
sum_square(t, p);
```

```plantuml
abstract class ISquare
{
	vptr
	X reale type
}

class vtable
{
	&X::square
	nullptr
}

ISquare <|- vtable
```

Cтатическим типом для lhs и rhs является известный на этапе компиляции тип const ISquare&. При этом в конкретном вызове у них могут быть разные динамические типы

# Проблема преобразований
При одиночном наследовании, чтобы преобразовать вверх или вниз по указателю или ссылке достаточно static_cast


![[../Files/Pasted image 20220423170856.png]]

```cpp
struct Base {};  
struct Derived : public Base {};  
Derived *pd = new Derived{};  
Base *pb = static_cast<Base*>(pd); // ok, можно просто присвоить, может статикаст, это не ошибка 
pd = static_cast<Derived*>(pb); // ok
```


При множественном наследовании также все работает

```cpp
struct Filler {  
    int x, y;  
    virtual void filler() = 0;  
    virtual ~Filler() {}  
};  
  
struct InputFile {  
    int b;  
    InputFile(int b) : b{b} {}  
    virtual ~InputFile() {}  
};  
  
struct OutputFile {  
    int c;  
    OutputFile(int c) : c{c} {}  
    virtual ~OutputFile() {}  
};  
  
struct IOFile : public Filler, public InputFile, public OutputFile {  
    int d;  
    void filler() override { std::cout << "  " << std::endl; }  
    IOFile(int d) : InputFile(d * 2), OutputFile(d * 3), d{d} {}  
};  
  
int main() {  
    IOFile *piof = new IOFile{5};  
    std::cout << std::hex << "piof = " << piof << ": " << std::dec;  
    std::cout << piof->d << std::endl;  
  
    OutputFile *pof = static_cast<OutputFile *>(piof);  
    std::cout << std::hex << "pof = " << pof << ": " << std::dec;  
    std::cout << pof->c << std::endl;  
  
    InputFile *pif = static_cast<InputFile *>(piof);  
    std::cout << std::hex << "pif = " << pif << ": " << std::dec;  
    std::cout << pif->b << std::endl;  
  
    piof = static_cast<IOFile *>(pif);  
    std::cout << std::hex << "piof = " << piof << ": " << std::dec;  
    std::cout << piof->d << std::endl;  
  
    piof = static_cast<IOFile *>(pof);  
    std::cout << std::hex << "piof = " << piof << ": " << std::dec;  
    std::cout << piof->d << std::endl;  
}
piof = 0x562039700eb0: 5
pof =  0x562039700ed0: 15//понимает, что нужно прибавить 16 в обоих случаях
pif =  0x562039700ec0: 10
piof = 0x562039700eb0: 5//и понимает, что нужно отнять
piof = 0x562039700eb0: 5
```

![[../Files/Pasted image 20220423170932.png]]

Но при виртуальном множественном наследовании нет

```cpp
struct File {  
    int a;  
    File(int a) : a{a} { std::cout << "File ctor" << std::endl; }  
    virtual ~File() {}  
};  
  
struct InputFile : virtual public File {  
    int b;  
    InputFile(int b) : File(b * 2), b{b} {  
        std::cout << "IFile ctor" << std::endl;  
    }  
};  
  
struct OutputFile : virtual public File {  
    int c;  
    OutputFile(int c) : File(c * 3), c{c} {  
        std::cout << "OFile ctor" << std::endl;  
    }  
};  
  
struct IOFile : public InputFile, public OutputFile {  
    int d;  
    IOFile(int d) : File(d), InputFile(d * 5), OutputFile(d * 7) {  
        std::cout << "IOFile ctor" << std::endl;  
    }  
};  
  
int main() {  
    IOFile *pe = new IOFile{11};  
    std::cout << pe->a << std::endl;  
  
    InputFile *pg = static_cast<InputFile *>(pe);  
    std::cout << pg->a << std::endl;  
  
    File *pf = static_cast<File *>(pg);  
    std::cout << pf->a << std::endl;  
  
    pe = static_cast<IOFile *>(pf);//error: cannot convert from pointer to base class ‘File’ to pointer to derived class ‘IOFile’ because the base is virtual поскольку непонятно сколько отступать  
    std::cout << pe->a << std::endl;  
}
```

