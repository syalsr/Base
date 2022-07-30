# Наследование
При наследовании наследуются все публичные и protected методы и данные класса. Наследовать можно только 1 класс
```java
class Animal{  
    public void show(){}  
}  
class Dog extends Animal{  
    @Override  
    public void show(){}  
}
```