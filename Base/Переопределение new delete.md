# Переопределение new/delete
## Перегрузка new && delete
Что делает new?
1. Выделяет память
2. Вызывает конструктор объекта
3. Возвращает указатель
```cpp
void* operator new(size_t size);
void* operator new[](size_t size);
void* operator new(size_t size, const std::nothrow_t&) noexcept;
void* operator new[](size_t size, const std::nothrow_t&) noexcept;
```

Что делает delete?
1. Вызывает деструктор для объекта
2. Очищает память
```cpp
void operator delete(void* ptr) noexcept;
void operator delete[](void* ptr) noexcept;
void operator delete(void* ptr, const std::nothrow_t&) noexcept;
void operator delete[](void* ptr, const std::nothrow_t&) noexcept;
void operator delete(void* ptr, void*) noexcept;
void operator delete[](void* ptr, void*) noexcept;
```

Перегрузки nothrow и placement delete используется в том случае, если конструктор выбрасывает исключение. В таком случае выбираем тот delete который соответствует new. Если мы удаляем объект как обычно `delete delete[]`, то вызовется обычная перегрузка(не nothrow и placement).

Стоит переопределять new и delete только для специфичных классов, при создании объекта этого класса будут вызываться переопределенные операторы. Если мы переопределяем new, то должны переопределить соответствующую форму оператора delete, иначе вызовется встроенный delete, который может неправильно очистить память.

Пример переопределения:

```cpp
class MemoryDemo
{
public:
	virtual ~MemoryDemo() = default;
	void* operator new(size_t size);
	void operator delete(void* ptr) noexcept;
	void* operator new[](size_t size);
	void operator delete[](void* ptr) noexcept;
	void* operator new(size_t size, const std::nothrow_t&) noexcept;
	void operator delete(void* ptr, const std::nothrow_t&) noexcept;
	void* operator new[](size_t size, const std::nothrow_t&) noexcept;
	void operator delete[](void* ptr, const std::nothrow_t&) noexcept;
};

void* MemoryDemo::operator new(size_t size)
{
	cout << "operator new" << endl;
	return ::operator new(size);
}
void MemoryDemo::operator delete(void* ptr) noexcept
{
	cout << "operator delete" << endl;
	::operator delete(ptr);
}
void* MemoryDemo::operator new[](size_t size)
{
	cout << "operator new[]" << endl;
	return ::operator new[](size);
}
void MemoryDemo::operator delete[](void* ptr) noexcept
{
	cout << "operator delete[]" << endl;
	::operator delete[](ptr);
}
void* MemoryDemo::operator new(size_t size, const nothrow_t&) noexcept
{
	cout << "operator new nothrow" << endl;
	return ::operator new(size, nothrow);
}
void MemoryDemo::operator delete(void* ptr, const nothrow_t&) noexcept
{
	cout << "operator delete nothrow" << endl;
	::operator delete(ptr, nothrow);
}
void* MemoryDemo::operator new[](size_t size, const nothrow_t&) noexcept
{
	cout << "operator new[] nothrow" << endl;
	return ::operator new[](size, nothrow);
}
void MemoryDemo::operator delete[](void* ptr, const nothrow_t&) noexcept
{
	cout << "operator delete[] nothrow" << endl;
	::operator delete[](ptr, nothrow);
}

int main()
{
	MemoryDemo* mem{ new MemoryDemo{} };
	delete mem;
	mem = new MemoryDemo[10];
	delete[] mem;
	mem = new (nothrow) MemoryDemo{};
	delete mem;
	mem = new (nothrow) MemoryDemo[10];
	delete[] mem;
}
/*
	operator new
	operator delete
	operator new[]
	operator delete[]
	operator new nothrow
	operator delete
	operator new[] nothrow
	operator delete[]
*/
```

Мы можем явно сделать delete/default операторы new и delete

```cpp
class MyClass
{
public:
	void* operator new(size_t size) = delete;
	void* operator new[](size_t size) = delete;
};

int main()
{
	MyClass* p1 { new MyClass };//error
	MyClass* p2 { new MyClass[2] };//error
	//явно удалили операторы, теперь мы не сможем динамически создавать этот класс
}
```

Мы можем переопределить глобальный оператор

```cpp
void *operator new(std::size_t n) {  
    void *p = malloc(n);  
    if (!p)  
        throw std::bad_alloc{};  
    printf("Alloc: %p, size is %zu\n", p, n);  
    return p;  
}  
  
void operator delete(void *mem) noexcept {  
    printf("Free: %p\n", mem);  
    free(mem);  
}  
  
struct Widget {  
    static void *operator new(std::size_t n);  
    static void operator delete(void *mem) noexcept;  
    int n[4];  
};  
  
void *Widget::operator new(std::size_t n) {  
    void *p = malloc(n);  
    if (!p)  
        throw std::bad_alloc{};  
    printf("Custom alloc: %p, size is %zu\n", p, n);  
    return p;  
}  
  
void Widget::operator delete(void *mem) noexcept {  
    printf("Custom free: %p\n", mem);  
    free(mem);  
}  
  
int main() {  
    std::list<int> l;  
    l.push_back(42);  
    Widget *w = new Widget;  
    delete w;  
    //Alloc: 0x55b1f9183eb0, size is 24  
    //Custom alloc: 0x55b1f91842e0, size is 16    
    //Custom free: 0x55b1f91842e0    
    //Free: 0x55b1f9183eb0
}
```

При использовании new[] стандарт гарантирует, если вызовется исключение на n объекте, то все ранее созданные удалятся.

## Перегрузка операторов new и delete с extra параметрами
Мы можем определить оператор new/delete для класса и они могут иметь сколько угодно аргументов, т.е. в классе может быть сколько угодно перегрузок.

```cpp
class MemoryDemo
{
public:
	void* operator new(size_t size, int extra)
	{
		cout << "operator new with extra int: " << extra << endl;
		return ::operator new(size);
	}
	void operator delete(void* ptr, int extra) noexcept
	{
		cout << "operator delete with extra int: " << extra << endl;
		return ::operator delete(ptr);
	}
};

int main()
{
	MemoryDemo* memp { new(5) MemoryDemo{} };
	delete memp;//вызовется обычный delete
}
/*
	operator new with extra int: 5
	operator delete
*/
```

Если в вашем классе объявлены две версии delete - одна с параметром size, другая нет, то всегда будет вызываться версия без параметра. Чтобы этого избежать нужно объявить только версию с параметром
```cpp
void MemoryDemo::operator delete(void* ptr, size_t size) noexcept
{
	cout << "operator delete with size " << size << endl;
	::operator delete(ptr);//не существует глоабльного delete который принимал бы размер
}
```