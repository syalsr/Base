# std::unordered_set
```cpp
int main () {  
    {  
        std::set<int> s = {67, 42, 141, 23, 42, 106, 15, 50};  
        auto itb = s.lower_bound(30);  
        auto ite = s.upper_bound(100);  
  
        for (auto it = itb; it != ite; ++it)  
            std::cout << *it << " ";//42 50 67  
        cout << endl;  
    }  
    {  
        std::set<int, std::greater<int>> s = {  
                67, 42, 141, 23, 42, 106, 15, 50};  
        auto itb = s.lower_bound(30);  
        auto ite = s.upper_bound(100);  
  
        for (auto it = itb; it != ite; ++it)  
            std::cout << *it << " ";//5 7 15 7 15 7 15 7 15 7 15 7 15 7 15... и т.д.  
            //[[итераторы]] не валидны из-за другого сравнения    }  
    {  
        std::set<int, std::greater<int>> s = {  
                67, 42, 141, 23, 42, 106, 15, 50};  
        auto itb = s.lower_bound(100);  
        auto ite = s.upper_bound(30);  
  
        for (auto it = itb; it != ite; ++it)  
            std::cout << *it << " ";//67 50 42  
        cout << endl;  
    }  
    {  
        std::set<int, std::less_equal<int>> s = {  
                67, 42, 141, 23, 42, 106, 15, 50};  
        auto itb = s.lower_bound(30);  
        auto ite = s.upper_bound(100);  
  
        for (auto it = itb; it != ite; ++it)  
            std::cout << *it << " ";//42 42 50 67 нарушен инвариант контейнера UB 
        cout << endl;  
    }  
}
```

# unordered_multiset
В отличии от multiset, не сортирует элементы.