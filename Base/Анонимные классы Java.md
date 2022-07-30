# Анонимные классы
Возможность языка in place переопределить метод.

```java
class Animal{
	public void eat(){}
}

Animal animal = new Animal(){
	public void eat(){}
};

animal.eat();//Вызовется переопределенный метод
```

В animal лежит не объект класса Animal, а его наследник.

```java
interface AbleToEat{
	public void eat();
}

AbleToEat ableToEat = new AbleToEat(){
	@Override
	public void eat(){}
};
```
