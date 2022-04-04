# Explicit constructor
Исключает неявные преобразования параметров конструктора

```cpp
struct Date
{
	int day, month, year;
};
class A
{
public:
	explicit A(Date date)
	{
		cout << 1;
	}
private:
	int data=5;
};
void test(const A& a) {}
int main()
{
	A a({1,1,2000});//OK
	A a {1,1,2000};//error
	test({ 1,1,2000 });//error
}
```

Для избегания 1 случая всегда использовать [[Initialization#uniform]]