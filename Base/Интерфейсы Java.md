# Интерфейсы
Интерфейс определяет какие методы должны реализовывать классы, по умолчанию они все public abstract
```java
public interface Info{
	void showInfo();
}

public class Animal implements Info{
	public void showInfo(){}
}
public class Person implements Info{
	public void showInfo(){}
}

Info info = new Animal();//info имеет доступ только к методом интерфейса
```

Если класс имплементирует какой-то интерфейс, то он должен определить все методы этого интерфейса. Класс может наследовать любое количество интерфейсов.

# Функциональный интерфейс
Это интерфейс у которого лишь 1 метод, для того, чтобы это отслеживать существует аннотация `@FunctionalInterface`

```java
@FunctionalInterface
public interface Interf{
	void method();
}
```

Он нужен для того, чтобы создать [[Лямбда выражения Java]]

```java
@FunctionalInterface
public interface Interf{
	void method();
}

Interf i = () -> {
	System.out.println(1);
};
i.method();
```

# Почему в некоторых интерфейсах вообще не определяют методов
Это так называемые _маркерные интерфейсы_. Они просто указывают что класс относится к определенному типу. Примером может послужить интерфейс `Clonable`, который указывает на то, что класс поддерживает механизм клонирования.

* [[Отличие интерфейса от абстрактного класса]]
