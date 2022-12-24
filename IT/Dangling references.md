# Висячие ссылки dangling reference
```cpp
const int& clref (int p) { return p + 0; } // p + 0 is dead 

int x = clref(2); // значение x не определено 
const int& cx = clref(3); // значение cx не определено

//Провиснуть могут все типы ссылок
const int& clref(int p) { return p + 0; } // p + 0 is dead
const int& clref(int p) { return p; } // p rests in peace
int& lref(int p) { return p; } // p is p no more
int&& rref(int p) { return p + 0; } // p + 0 has expired and gone
int&& rvref(int&& p) { return p + 0; } // p + 0 is an ex-p + 0

//• Есть неожиданно корректные случаи, когда identity сохраняется в фрейме вызова
int& lvref(int& x) { return x; } // ok
const int& clvref(const int& x) { return x; } // ok
```

Как связаться с lvalue?

```cpp
int x = 42;
int&& rvx = std::move(x);
rvx += 2;
assert(x == 44);
```

![[../Files/Pasted image 20220426181353.png]]

```cpp
struct RBind {  
    int& ref;  
    RBind(int&& r) : ref(r) {}  
    RBind const& plus(int x) const { ref += x; return *this; }  
    RBind const& mult(int x) const { ref *= x; return *this; }  
    int get() const { return ref; }  
};  
int x = 1;  
cout << RBind(1).plus(2).mult(3).get() << endl; // 9  
cout << RBind(move(x)).plus(2).mult(3).get() << endl; // 9  
cout << RBind(x + 0).plus(2).mult(3).get() << endl; // 33
```

Конструктор берущий rvalue ref не обязан сохранять значение (т.к. это rvalue)

```cpp
class Buffer {  
    size_t sz; int *buf;  
public:  
    Buffer (Buffer&& rhs) : sz(rhs.sz), buf(rhs.buf) {  
        rhs.sz = 0; rhs.buf = nullptr;  
    }  
    Buffer (const Buffer& rhs) : sz(rhs.sz), buf(new int[sz]) {  
        copy (rhs.buf, rhs.buf + sz, buf);  
    }  
};
```

Конструктор копирования (аналогично ведёт себя копирующее присваивание) 
1. deleted если его нет, но есть пользовательское перемещение или move ctor 
2. иначе defaulted если его нет (но если при этом есть деструктор или парный копирующий метод, то это deprecated)

Перемещающий конструктор (аналогично перемещающее присваивание) 
1. deleted если его нет, но при этом есть парный перемещающий метод или copy ctor или копирующее присваивание или деструктор 
2. иначе defaulted если его нет