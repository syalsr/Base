# bidirectional list
Для каждого элемента свой блок памяти в котором хранятся значение и адрес следующего и предыдущего элементов.

![[../Files/Pasted image 20220216143504.png]]

С такой реализацией можно быстро удалять элементы из любой позиции, все указатели и [[итераторы]] останутся валидными, если элемент на который они указывали, не удалили.

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
int main()
{
	// create two empty lists
	list<int> list1, list2;
	// fill both lists with elements
	for (int i=0; i<6; ++i) {
		list1.push_back(i);
		list2.push_front(i);
	}
	printLists(list1, list2);
	// insert all elements of list1 before the first element with value 3 of list2
	// - find() returns an iterator to the first element with value 3
	list2.splice(find(list2.begin(),list2.end(), // destination position
						3),
					list1); // source list
	printLists(list1, list2);
	// move first element of list2 to the end
	list2.splice(list2.end(), // destination position
					list2, // source list
					list2.begin()); // source position
	printLists(list1, list2);
	// sort second list, assign to list1 and remove duplicates
	list2.sort();
	list1 = list2;
	list2.unique();
	printLists(list1, list2);
	// merge both sorted lists into the first list
	list1.merge(list2);
	printLists(list1, list2);
	list1: 0 1 2 3 4 5
	list2: 5 4 3 2 1 0
	list1:
	list2: 5 4 0 1 2 3 4 5 3 2 1 0
	list1:
	list2: 4 0 1 2 3 4 5 3 2 1 0 5
	list1: 0 0 1 1 2 2 3 3 4 4 5 5
	list2: 0 1 2 3 4 5
	list1: 0 0 0 1 1 1 2 2 2 3 3 3 4 4 4 5 5 5
	list2:
}
```

## Когда использовать?
1. Когда хотим сохранить валидность итераторов