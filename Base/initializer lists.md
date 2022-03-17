С помощью данного шаблона можно передавать в функцию переменное количество параметров одного типа.

```cpp
#include <initializer_list>;
using namespace std;
int makeSum(initializer_list<int> values)
{
	int total { 0 };
	for (int value : values) {
		total += value;
	}
	return total;
}
int a { makeSum({ 1, 2, 3 }) };
int b { makeSum({ 10, 20, 30, 40, 50, 60 }) };
```

