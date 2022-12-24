# Array
Это вектор, но память выделяется на стеке(если это возможно), поэтому про объявлении мы должны указать тип и размер, обертка над [[Модель памяти#arrays в pure C|сишным массивом]]. Поддерживает [[Категории итераторов#Random access iterators]], поскольку имеет фиксированный массив реалокаций не происходит.
```cpp
int main()
{
    array<int, 3> arr{ 9, 8, 7 };
    cout << "Array size = " << arr.size() << endl;
    for (const auto& i : arr) {
        cout << i << endl;
    }
    cout << arr[0] << " " << get<0>(arr) << "\n";
    cout << "Performing arr.fill(3)..." << endl;
    arr.fill(3);
    for (auto iter{ cbegin(arr) }; iter != cend(arr); ++iter) {
        cout << *iter << endl;
    }
    std::array<int,10> c2 = { 42 }; // one element with value 42
									// followed by 9 elements with value 0
	std::array<int,5> c3 = { 1, 2, 3, 4, 5, 6 }; // ERROR: too many values
	std::array<char, 41> a; // create static array of 41 chars
	strcpy_s(a.data(), 13,"hello, world"); // copy a C-string into the array
	printf("%s\n", &a[0]); // print contents of the array as C-string
}
/*
    Array size = 3
    9
    8
    7
    9 9
    Performing arr.fill(3)...
    3
    3
    3
*/
```