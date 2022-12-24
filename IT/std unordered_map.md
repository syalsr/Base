# std::unordered_map
Этот контейнер отличается от map, тем что здесь ключи не сортируются, соответственно если нам не важен порядок, то можем выбрать в этот контейнер для большей производительности.
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

# std::unordered_multimap
Этот контейнер хранит неупорядоченные элементы с одинаковыми ключами, поэтому как и в обычном multimap не поддерживает `operator[] at()`, `insert` всегда успешен, для поиска элементов нужно использовать `equal_range()`