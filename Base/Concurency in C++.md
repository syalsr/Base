# Concurency
* [[async и future]]
* [[mutex и lock_guard]]
* [[Константность в многопоточных программах]]
* [[mutable в многопоточных программах]]
* [[Ошибки в многопоточных программах]]

```cpp
#include <iostream>
#include <thread>
void hello()
{
	std::cout<<"Hello Concurrent World\n";
}
int main()
{
	std::thread t(hello);
	t.join();
}
```