# reference переменные
```cpp
class B {
public:
	B() : a(5) {}
	void set(int aa) {
		a = aa;
	}
private:
	int a;
};
class A
{
public:
	A(B& b) : b_(b)
	{}
	void set()
	{
		b_.set(43);
	}
private:
	B& b_;
};


int main()
{
	B b;//a=5
	b.set(12);//a=12
	A a{ b };
	a.set();//B::a=43
}
```