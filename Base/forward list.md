# Forward list
Для каждого элемента свой блок памяти в котором хранятся значение и адрес следующего элемента. Поддерживает [[Категории итераторов#Forward iterators]], т.е. может ходить только в одном направлении

![[../Files/Pasted image 20220216143448.png]]

```cpp
int main()
{
    forward_list<int> list1{ 5, 6 };
    forward_list list2{ 1, 2, 3, 4 };
    forward_list list3{ 7, 8, 9 };
    
    list1.splice_after(list1.before_begin(), list2);
    list1.push_front(0);

    auto iter{ list1.before_begin() };//из-за того что нет операции -- не можем сделать prev(list1.end())
    auto iterTemp{ iter };
    while (++iterTemp != end(list1)) { ++iter; }
    list1.insert_after(iter, cbegin(list3), cend(list3));
    
    for (auto& i : list1) { cout << i << ' '; }
}
```

```cpp
forward_list<int> fst = { 1, 2, 3 };  
forward_list<int> snd = { 10, 20, 30 };  
auto it = fst.begin(); // указывает на 1  
// перемещаем second в начало first, it указывает на 1  
fst.splice_after(fst.before_begin(), snd);//ПРОСТАЯ ФОРМА O(1)
```

![[../Files/Pasted image 20220425141648.png]]

```cpp
// перекидываем элементы со второго по it в список second 
snd.splice_after(snd.before_begin(), fst, fst.begin(), it);//СЛОЖНАЯ ФОРМА O(N)
//т.к. [[итераторы]] не инвалидируются, используем старый который также указывает на значение 1
```

![[../Files/Pasted image 20220425141947.png]]

```cpp
// все элементы второго списка начиная со второго в первый 
fst.splice_after(fst.before_begin(), snd, snd.begin());//СРЕДНЯЯ ФОРМА O(1)
```

![[../Files/Pasted image 20220425142150.png]]

```cpp
int main() {  
    std::ostream_iterator<int> icout{std::cout, " "};  
    std::forward_list<int> first = {1, 2, 3};  
    std::forward_list<int> second = {10, 20, 30};  
    auto it = first.begin();  
  
    // splice second to first: note special before_begin insert position  
    first.splice_after(first.before_begin(), second);  
    std::cout << "1." << std::endl;  
    std::copy(first.begin(), first.end(), icout); std::cout << std::endl;  
  
    second.splice_after(second.before_begin(), first, first.begin(), it);  
    std::cout << "2." << std::endl;  
    std::copy(first.begin(), first.end(), icout); std::cout << std::endl;  
    std::copy(second.begin(), second.end(), icout); std::cout << std::endl;  
  
    first.splice_after(first.before_begin(), second, second.begin());  
    std::cout << "3." << std::endl;  
    std::copy(first.begin(), first.end(), icout); std::cout << std::endl;  
    std::copy(second.begin(), second.end(), icout); std::cout << std::endl;  
    //1.  
    //10 20 30 1 2 3    
    //2.    
    //10 1 2 3    
    //20 30    
    //3.    
    //30 10 1 2 3    
    //20
}
```

## Как и когда использовать forward_list
1. Когда нужно экономить память
2. Сохранить валидность итераторов