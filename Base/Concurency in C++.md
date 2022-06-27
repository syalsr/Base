# Concurency
* [[async и future]]
* [[mutex и lock_guard]]
* [[Константность в многопоточных программах]]
* [[mutable в многопоточных программах]]

```cpp
void process()
{
	for(int...)
		std::cout << "+";
		
}

int main()
{
	std::thread thr(process);
	for(int i...)
		std::cout << "-";
	thr.join();//чтобы программа не упала
}
```