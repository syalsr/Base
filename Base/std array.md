# Array
Это вектор, но память выделяется на стеке, поэтому про объявлении мы должны указать тип и размер, обертка над [[C style arrays|сишным массивом]]. Поддерживает [[Категории итераторов#Random access iterators]]
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