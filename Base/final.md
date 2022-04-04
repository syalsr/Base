# final
Чтобы запретить классам наследоваться от класса или переопределять метод, его нужно пометить как final

```cpp
class A final
{

}
class B : A//error
{

}


class A
{
	virtual void say();
}
class B final: public A
{
	void say() override final;
}
class C : B//error
{
	void say() override;//error
}
```

Также методы можно пометить как final, чтобы нельзя было переопределять их в производных классах.