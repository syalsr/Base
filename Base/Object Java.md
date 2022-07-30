# Object
`Object` это базовый класс для всех остальных объектов в Java. Любой класс наследуется от `Object` и, соответственно, наследуют его методы.

# Методы
## public boolean equals(Object obj) 
Служит для сравнения объектов по значению; 

## int hashCode() 
Возвращает hash код для объекта;

## String toString() 
Возвращает строковое представление объекта; 
```java
Person p1 = new Person("Bob", 42);
System.out.println(p1);//у p1 автоматически вызывается метод toString() класса Object, выведется хеш код объекта
//Person@3cd1a2f1

class Person{
	private String name;
	private int age;
	public Person(String name, int age){
		this.name = name;
		this.age = age;
	}
}

Person p1 = new Person("Bob", 42);
System.out.println(p1);//Bob 42

class Person{
	private String name;
	private int age;
	public Person(String name, int age){
		this.name = name;
		this.age = age;
	}
	public String toString(){
		return name + " " + age;
	}
}
```

## Class getClass() 
Возвращает класс объекта во время выполнения; 

## `protected Object clone()` – создает и возвращает копию объекта; 

## void notify() 
Возобновляет поток, ожидающий монитор; `void notifyAll()` – возобновляет все потоки, ожидающие монитор;

## void wait() 
Остановка вызвавшего метод потока до момента пока другой поток не вызовет метод `notify()` или `notifyAll()` для этого объекта;

## void wait(long timeout)
Остановка вызвавшего метод потока на определённое время или пока другой поток не вызовет метод `notify()` или `notifyAll()` для этого объекта;

## void wait(long timeout, int nanos) 
Остановка вызвавшего метод потока на определённое время или пока другой поток не вызовет метод `notify()` или `notifyAll()` для этого объекта;

## protected void finalize() 
Может вызываться сборщиком мусора в момент удаления объекта при сборке мусора.