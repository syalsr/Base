# bidirectional list
Для каждого элемента свой блок памяти в котором хранятся значение и адрес следующего и предыдущего элементов.

![[../Files/Pasted image 20220216143504.png]]

С такой реализацией можно быстро удалять элементы из любой позиции, все указатели и итераторы останутся валидными, если элемент на который они указывали, не удалили.

Поскольку блоки не расположены как у массива, для доступа к элементам вместо `[]` используются [[Категории итераторов#Bidirectional iterators]]

Итог: лист эффективнее вектора в удалении, допустим мы удаляем элементы в цикле, чем меньше элементов за цикл удаляется тем list эффективнее, поскольку блоки не идут последовательно в памяти, а прыгать с блока на блок на эффективно

```cpp
int main()
{
    list<int> leaf;
    leaf.push_back(1);
    leaf.push_front(2);
    leaf.pop_back();
    leaf.pop_front();
    leaf.emplace_back(1);
    leaf.emplace(leaf.begin(), 3);
    leaf.emplace_front(4);
    leaf.clear();
}
```

## Splicing
Перемещает элементы из одного list в другой, list из которого перемещаются элементы становится пустым, т.к. одна нода это указатель на следующую и предыдущую, довольно эффективно вставлять в любую точку.
```cpp
int main()
{
    list<string> dictionary{ "aardvark", "ambulance" };
    list<string> bWords{ "bathos", "balderdash" };
    dictionary.push_back("canticle");
    dictionary.push_back("consumerism");
    // Splice the b words into the main dictionary.
    if (!bWords.empty()) {
        // Get an iterator to the last b word.
        auto iterLastB{ --(cend(bWords)) };
        // Iterate up to the spot where we want to insert b words.
        auto it{ cbegin(dictionary) };
        for (; it != cend(dictionary); ++it) {
            if (*it > *iterLastB)
                break;
        }
        // Add in the b words.This action removes the elements from bWords.
            dictionary.splice(it, bWords);
    }
    // Print out the dictionary.
    for (const auto& word : dictionary) {
        cout << word << endl;
    }
}
```

## Когда использовать?
1. Когда хотим сохранить валидность итераторов

