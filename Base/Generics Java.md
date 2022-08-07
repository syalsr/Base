# Generics Java
-   Generics. Параметризация кода
-   Wildcards. Параметризация и наследование. Принцип PECS

```java
List<String> animals = new ArrayList<String>();
List<String> animals = new ArrayList<>();
animals.add("dog");
animals.add("cat");
String animal = animals.get(0);
```

# Параметризация на уровне класса
```java
public abstract class Hero<T>{
	private String name;
	private int damage;
	private T weapon;

	public T getWeapon(){
		return weapon
	}
}

public class Archer<T> extends Hero<T>{

}
```

# Ограничение для шаблонов extends
```java
public abstract class Hero<T extends Weapon>{
	private String name;
	private int damage;
	private T weapon;

	public T getWeapon(){
		return weapon
	}
}

public class Archer<T extends RangeWeapon> extends Hero<T>{

}
```

# Параметризация на уровне метода
```java
public static <T extends Weapon> void printWeaponDamage(Hero<T> hero){
...
}
```

#  Wildcards
Wildcards - это упрощенный синтаксис параметризации методов.
```java
private static void test(List<?> list){
	for(Animal animal : list){
		System.out.println(animal);
	}
}
```
С помощью ? мы хотим, чтобы test принимал List с любыми типами, но тогда у нас возникает проблемы в цикле for, т.к. тип может быть любой, то мы не знаем какой он и не можем написать Animal.

```java
private static void test(List<? extends Animal> list){
	for(Animal animal : list){
		System.out.println(animal);
	}
}
```

Теперь с помощью extends мы говорим, что тип наследуется от Animal и мы можем передать Animal, либо его потомки

```java
private static void test(List<? super Animal> list){
	for(Animal animal : list){
		System.out.println(animal);
	}
}
```

С помощью super, мы можем передать либо Animal, либо его родители, в данном случае либо Animal, либо Object.