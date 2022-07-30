# Сравнение объектов
При сравнении объектов с помощью `==` сравниваются их указатели. Для правильного сравнения нужно переопределять для каждого класса метод `equals(Object obj)` из класса `Object`.

```java
class Animal{
	private int id;
	public boolean equals(Object obj){
		Animal other = (Animal) obj;
		return this.id == other.id;
	}
}
```
