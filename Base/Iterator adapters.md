# Итераторы inserter && back_inserter && front_inserter
```cpp
vector<string> langs = {"Python", "C++", "C", "Java", "C#"}; 
vector<string> c_langs; // о размере уже не беспокоимся 
copy_if(begin(langs), end(langs), 
		back_inserter(c_langs), // специальный итератор, вставляющий в конец 
		[](const string& lang) { // привычная лямбда-функция 
		return lang [0] == 'C'; // снова выделяем только начинающиеся на C }); 
PrintRange (begin(c_langs), end(c_langs)); // C++, C, C#,
```

Итератор `back_inserter` помимо того, что умеют делать обычные итераторы, еще делает push_back в контейнер к которому относится.

```cpp
template <typename InputIter, typename OutputIter>
void myCopy(InputIter begin, InputIter end, OutputIter target)
{
	for (auto iter { begin }; iter != end; ++iter, ++target) { *target = *iter; }
}
int main()
{
	vector vectorOne { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
	vector<int> vectorTwo;
	
	myCopy(cbegin(vectorOne), cend(vectorOne), back_inserter(vectorTwo));
	
	myCopy(cbegin(vectorTwo), cend(vectorTwo), ostream_iterator<int> { cout, " " });
}
```

`front_inserter` - вставляет в начало.

`inserter` возвращает специальный итератор, который вставляет элемент в множество, конструктор принимает контейнер и позицию

```cpp
set<int> a = {1, 8 ,3};
set<int> b = {3, 6 ,8};
set<int> res; 
auto it = set_intersection (begin(a), end(a), begin(b), end(b), inserter(res , end(res)));
PrintRange (begin(res), end(res));
```


```cpp
int main()
{
    std::multiset<int> s{ 1, 2, 3 };

    // std::inserter is commonly used with multi-sets
    std::fill_n(std::inserter(s, s.end()), 5, 2);

    for (int n : s)
        std::cout << n << ' ';
    std::cout << '\n';

    std::vector<int> d{ 100, 200, 300 };
    std::vector<int> v{ 1, 2, 3, 4, 5 };

    // when inserting in a sequence container, insertion point advances
    // because each std::insert_iterator::operator= updates the target iterator
    std::copy(d.begin(), d.end(), std::inserter(v, std::next(v.begin())));

    for (int n : v)
        std::cout << n << ' ';
    std::cout << '\n';
	//1 2 2 2 2 2 2 3
	//1 100 200 300 2 3 4 5
}
```