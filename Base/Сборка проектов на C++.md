# Препроцессинг
# Linkage
Исходные файлы C++ компилируются независимо друг от друга. В результате компиляции файлов получаются объектные файлы который линковщик должен объединить в исполняемый файл. Каждое имя в исходном файле имеет внешнюю связь - доступно в других файлах и внутреннюю(статическую) связь - не доступно в других файлах.

## static
По умолчанию функции и глобальные переменные имеют внешнюю связь, мы можем это изменить приписав им ключевое слово static.

Что значит внутреннюю? То есть если у нас в одном файле объявлена переменная, мы подключаем ее в 2 других файла и в этих файлах будет копия это переменной, а не она сама

```cpp
//header.hpp:    
static int variable = 42;
//file1.hpp
void function1();
//file2.hpp
void function2();
//file1.cpp
#include "header.hpp" 
void function1() { variable = 10; }
//file2.cpp
#include "header.hpp" 
void function2() { variable = 123; }
//main.cpp
#include "header.hpp" 
#include "file1.hpp" 
#include "file2.hpp" 
#include <iostream> 
auto main() -> int 
{ 
	function1(); //variable = 10
	function2(); //variable = 123
	std::cout << variable << std::endl;//output 42 
}
```

Можно также использовать анонимные пространства имен, делают тоже самое, что и static

```cpp
namespace
{
	int variable = 42;
}
```

Для использования внутренних связей рекомендуется использовать анонимный namespace

## extern
Предоставляет внешнюю линковку, используется для объявления переменной, т.е. компилятор не будет выделять место, это мы должны сделать

```cpp
extern int x;
int x { 3 };
---
extern int x { 3 };
```

#do/start начать конспектировать https://www.youtube.com/playlist?list=PL3BR09unfgchnggx7IJuSU57mxjMhrSaq