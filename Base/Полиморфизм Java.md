# Полиморфизм
Это способность объекта менять свое поведение.

```java
class Animal{  
    public void show(){}  
}  
class Dog extends Animal{  
    @Override  
    public void show(){}  
    public void test(){}
}

Animal an = new Dog();
an.show();
an.test();//error, можем вызывать только родительские методы.
```

```java
public interface Info{
	public void showInfo();
}

public class Animal implements Info{
	public void showInfo(){}
}
public class Person implements Info{
	public void showInfo(){}
}

public class main{
	public polym(Info info){
		info.showInfo();//в зависимости от переданного объекта, выведется разная информация
	}
}
```
