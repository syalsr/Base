# Enum Java
Иерархия наследования
![[enum.excalidraw]]
```java
public class main {  
    public static void main(String[] args) {  
        Animal animal = Animal.DOG;  
        switch (animal){  
            case CAT -> System.out.println("cat");  
            case DOG -> System.out.println("dog");  
            case FROG -> System.out.println("frog");  
        }  
    }  
  
}  
  
enum Animal{  
    CAT, DOG, FROG  
}
```

```java
public class main {  
    public static void main(String[] args) {  
        Animal animal1 = Animal.FROG;  
        System.out.println(animal1.name());//FROG  
        for(Animal animal : Animal.values()){  
            System.out.println(animal);  
        }  
        /*  
        CAT        
        DOG        
        FROG         
        */    
    }  
  
}  
  
  
enum Animal{  
    CAT("кот"), DOG("собака"), FROG("лягушка");  
  
    private String trans;  
    private Animal(String trans){//констурктор приватный, т.к. нам не нужно создать его где-то вне  
        this.trans = trans;  
    }  
  
    public String getTrans(){  
        return trans;  
    }  
    public String toString(){  
        return trans;  
    }  
}
```

```java
String str = "CAT";  
Animal animal = Animal.valueOf(str);  
System.out.println(animal.name() + " " + animal.toString());//CAT кот
System.out.println(animal.ordinal());//0 - выводим индекс
```

По умолчанию `ordinal` первого элемента $=0$, а для остальных инкрементируется. Enum может имплементировать [[Интерфейсы Java]]