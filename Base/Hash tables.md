# Unordered Associative Containers OR Hash tables
# Hash functions
Этот контейнер отличается от map, тем что здесь ключи не сортируются, соответственно если нам не важен порядок, то можем выбрать в этот контейнер для большей производительности.

unordered контейнеры используют хеш-функции для составления хеш-таблицы на основе которых хранятся данные. Допустим нам нужно хранить автомобильные номера, в таком случае хеш-функцией может выступать номер региона, если все номера будут одного региона, то будет крайне не производительно, а если мы будем умножать простое число на каждый элемент номера, то получится уникальное число.

Хеш-таблица состоит из нескольких корзин по которым раскладываются объекты на основе хеш-функции. Если корзина занята другим объектом, то происходит коллизия. Для разрешения данной проблем используется метод цепочек, корзина состоит из списка объектов, сначала проверяется, что объекта нет в корзине, потом он добавляется по цепочке. Чем длиннее цепочка, тем менее производительнее таблица.

![[../Files/Pasted image 20220408083258.png]]

Для того, чтобы цепочки в корзине были короткими при добавлении нового объекта может случиться рехеширование в результате чего итератор инвалидируются. Удалять итераторы можно не опасаясь за итераторы на другие элементы.

Стандарт C++ для всех встроенных типов данных представляет хеш функции, а также для `error_code, error_condition, optional, variant, bitset, unique_ptr, shared_ptr, type_index, string, string_view, vector<bool>, and thread::id`. Если в ассоциативном контейнере мы используем пользовательский тип данных, то хеш-функцию должны написать сами. Для встроенных типов данных уже есть хеш-функции(`std::hash<double> dhash`)

```cpp
struct Plate { 
	char C1; 
	int Number; 
	char C2; 
	char C3;
	int Region; 
};
bool operator==(const Plate& l, const Plate& r) { 
	return (l.C1 == r.C1) && (l.Number == r.Number) && (l.C2 == r.C2) && (l.C3 == r.C3) && (l.Region == r.Region); 
}
struct PlateHasher {
	size_t x = 9973;
	size_t operator() (const Plate& p) const { 
		size_t result = x * x * ihash(p.Number) + x * ihash(p.Region) + x * chash(p.C1) + x * chash(p.C2) + x * chash(p.C3);
		return result;
	}
	hash<int> ihash;
	hash<char> chash;
};
int main() {
	unordered_set<Plate, PlateHasher> h_plates; 
}
```

# unordered_map
Отличие от обычно map, в неупорядоченности элементов.
```cpp
unordered_map<int, string> m {
	{1, "Item 1"},
	{2, "Item 2"},
	{3, "Item 3"},
	{4, "Item 4"}
```

Разница между map и unordered_map(1 - контейнер поддерживает операцию, 0 - контейнер не поддерживает операцию)

| OPERATION                                 | map | unordered_map |
| ----------------------------------------- | --- | ------------- |
| at()                                      | 1   | 1             |
| begin()                                   | 1   | 1             |
| begin(n)                                  | 0   | 1             |
| bucket()                                  | 0   | 1             |
| bucket_count()                            | 0   | 1             |
| bucket_size()                             | 0   | 1             |
| cbegin()                                  | 1   | 1             |
| cbegin(n)                                 | 0   | 1             |
| cend()                                    | 1   | 1             |
| cend(n)                                   | 0   | 1             |
| clear()                                   | 1   | 1             |
| contains()                                | 1   | 1             |
| count()                                   | 1   | 1             |
| crbegin()                                 | 1   | 0             |
| crend()                                   | 1   | 0             |
| emplace()                                 | 1   | 1             |
| emplace_hint()                            | 1   | 1             |
| empty()                                   | 1   | 1             |
| end()                                     | 1   | 1             |
| end(n)                                    | 0   | 1             |
| equal_range()                             | 1   | 1             |
| erase()                                   | 1   | 1             |
| extract()                                 | 1   | 1             |
| find()                                    | 1   | 1             |
| insert()                                  | 1   | 1             |
| insert_or_assign()                        | 1   | 1             |
| iterator / const_iterator                 | 1   | 1             |
| load_factor()                             | 0   | 1             |
| local_iterator / const_local_iterator     | 0   | 1             |
| lower_bound()                             | 1   | 0             |
| max_bucket_count()                        | 0   | 1             |
| max_load_factor()                         | 0   | 1             |
| max_size()                                | ■   | 1             |
| merge()                                   | 1   | 1             |
| operator[]                                | 1   | 1             |
| rbegin()                                  | 1   | 0             |
| rehash()                                  | 0   | 1             |
| rend()                                    | 1   | 0             |
| reserve()                                 | 0   | 1             |
| reverse_iterator / const_reverse_iterator | 1   | 0             |
| size()                                    | 1   | 1             |
| swap()                                    | 1   | 1             |
| try_emplace()                             | 1   | 1             |
| upper_bound()                             | 1   | 0             |

## example
```cpp
template<typename T>
void printMap(const T& m) // C++20 abbreviated function template
{
	for (auto& [key, value] : m) {
		cout << key << " (Phone: " << value << ")" << endl;
	}
	cout << "-------" << endl;
}
int main()
{
	// Create a hash table.
	unordered_map<string, string> phoneBook{
	{ "Marc G.", "123-456789" },
	{ "Scott K.", "654-987321" } };
	printMap(phoneBook);
	// Add/remove some phone numbers.
	phoneBook.insert(make_pair("John D.", "321-987654"));
	phoneBook["Johan G."] = "963-258147";
	phoneBook["Freddy K."] = "999-256256";
	phoneBook.erase("Freddy K.");
	printMap(phoneBook);
	// Find the bucket index for a specific key.
	const size_t bucket{ phoneBook.bucket("Marc G.") };
	cout << "Marc G. is in bucket " << bucket << "containing the following " << phoneBook.bucket_size(bucket) << "names:" << endl;
	// Get begin and end iterators for the elements in this bucket.
	// 'auto' is used here. The compiler deduces the type of
	// both as unordered_map<string, string>::const_local_iterator
	auto localBegin{ phoneBook.cbegin(bucket) };
	auto localEnd{ phoneBook.cend(bucket) };
	for (auto iter{ localBegin }; iter != localEnd; ++iter) {
		cout << "\t" << iter->first << " (Phone: " << iter->second << ")" << endl;
	}
	cout << "-------" << endl;
	// Print some statistics about the hash table
	cout << "There are " << phoneBook.bucket_count() << "buckets." << endl;
	cout << "Average number of elements in a bucket is " << phoneBook.load_factor() << "." << endl;
}

/*
	Scott K. (Phone: 654-987321)
	Marc G. (Phone: 123-456789)
	-------
	Scott K. (Phone: 654-987321)
	Marc G. (Phone: 123-456789)
	Johan G. (Phone: 963-258147)
	John D. (Phone: 321-987654)
	-------
	Marc G. is in bucket 1containing the following 2names:
	        Scott K. (Phone: 654-987321)
	        Marc G. (Phone: 123-456789)
	-------
	There are 8buckets.
	Average number of elements in a bucket is 0.5.
*/
```

# unordered_multimap
Этот контейнер хранит неупорядоченные элементы с одинаковыми ключами, поэтому как и в обычном multimap не поддерживает `operator[] at()`, `insert` всегда успешен, для поиска элементов нужно использовать `equal_range()`

# unordered_multiset
В отличии от multiset, не сортирует элементы.
