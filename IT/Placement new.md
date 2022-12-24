# operator placement new
Это функции которая на уже выделенной памяти вызывает конструктор объекта, чтобы занять ее, C++ запрещает перегружать их

```cpp
void* operator new(std::size_t size, void* ptr) noexcept; 
void* operator new[](std::size_t size, void* ptr) noexcept;

void *raw = ::operator new(sizeof(Widget), std::nothrow); 
if (!raw) { обработка } 
Widget *w = new (raw) Widget;//раз вызываем рука конструктор, то
//...
w->~Widget();//должны сами вызвать деструктор
::operator delete(raw);

int* n = new int(5);//выделяеям память и конструируем объект
n = (int *) ::operator new(sizeof(int));//используем глобальный оператор, происходит только выделение, отличается от malloc тем что может бросить исключение
```