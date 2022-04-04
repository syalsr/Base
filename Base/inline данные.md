# inline переменные
Можем пометить static переменные как inline. Преимущество в том, что не нужно для нее выделять память.

```cpp
class unique_ptr1
{
public:
	unique_ptr1() : id(counter++) {
	}
static inline size_t counter{0};
private:
	const size_t id;
};

int main()
{
	unique_ptr1 textHolder;
	unique_ptr1 textHolder1;
	unique_ptr1 textHolder2;
	int max_id = unique_ptr1::counter;
}
```