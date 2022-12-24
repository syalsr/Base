# RTTI - run time type information
Это динамическая идентификация типов во время выполнения программы, C++ содержит в себе дерево, чтобы каждый тип знал, свой тип, и в какой он может преобразоваться, т.е. знает своих родителей, детей,  поддерживается двумя функциями:
1. typeid - идентифицирует тип в ран тайме
2. [[casting C++#dynamic_cast|dynamic_cast]] - преобразует типы в ран тайме

Для классов с виртуальными методами они выполняются в ран тайме, для остальных типов в компайл тайме

```cpp
OutputFile *pof = new IOFile{5}; 
assert(typeid(*pof) == typeid(IOFile));// динамический тип
assert(typeid(pof) != typeid(IOFile*));// статический тип
```

Такое сравнение поскольку в указателях нет виртуальных функциях, они есть в самих объектах

```cpp
IOFile *piof = new IOFile{}; // File это виртуальная база  
File *pf = static_cast<File *>(piof); // ok  
InputFile *pif = dynamic_cast<InputFile *>(pf); // ok  
OutputFile *pof = dynamic_cast<OutputFile *>(pf); // ok  
pif = dynamic_cast<InputFile *>(pof); // ok!
```

Стоит использовать static_cast, поскольку у dynamic_cast высокие накладные расходы в зависимости от размера вашей иерархии. Но может случиться так, что вы вызовете dynamic_cast в том, месте где нужен static_cast, в таком случае компилятор постарается все сооптимизировать и будет использован static_cast.