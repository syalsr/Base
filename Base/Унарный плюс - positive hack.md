# Унарный плюс - positive hack
```cpp
2 == +2//true

struct Foo { operator long() { return 42; }};  
void foo(int x);  
void foo(Foo x);  
Foo f;  
foo(f); // вызовет foo(Foo)  
foo(+f); // вызовет foo(int) унарный + созданный компилятором приводит тип Foo к типу long, если мы сами захотим переопределить унарный + этого не будет, поэтому обычно егоне переопределяют 
```
