# Итераторы inserter && back_inserter
```cpp
#include ... 
vector langs = {"Python", "C++", "C", "Java", "C#"}; 
vector c_langs; // о размере уже не беспокоимся 
copy_if(begin(langs), end(langs), 
		back_inserter (c_langs), // специальный итератор, вставляющий в конец 
		[](const string& lang) { // привычная лямбда-функция 
		return lang [0] == 'C'; // снова выделяем только начинающиеся на C }); 
PrintRange (begin(c_langs), end(c_langs)); // C++, C, C#,
```

Итератор `back_inserter` помимо того, что умеют делать обычные итераторы, еще делает push_back в контейнер к которому относится.

```cpp
set<int> a = {1, 8 ,3};
set<int> b = {3, 6 ,8};
set<int> res; 
auto it = set_intersection (begin(a), end(a), begin(b), end(b), inserter(res , end(res))); // итератор для вставки в множество 
PrintRange (begin(res), end(res));
```

`inserter` возвращает специальный итератор, который вставляет элемент в множество.