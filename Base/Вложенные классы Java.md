# Вложенные классы
## private class
Используются когда хотим разделить большой и сложный класс на подклассы. В данном случае можно также использовать [[20 Paradigms#Отношение has-a]]
```java
class Electrocat{  
    private int id;  
  
    private class Engine{  
        public void startEngine(){}  
    }  
    public void start(){  
        Engine engine = new Engine();  
        engine.startEngine();  
    }  
}
```
Имеет доступ к приватным полям Electrocar.

## public static class
Вложенный класс может быть [[static]]. Используется когда класс является монолитом.
```java
class Electrocar{  
    private int id;  
  
    private class Engine{  
        public void startEngine(){}  
    }  
    public static class Battery{}  
    public void start(){  
        Engine engine = new Engine();  
        engine.startEngine();  
    }  
}  
  
Electrocar.Battery battery = new Electrocar.Battery();
```
Имеет доступ к приватным полям Electrocar.

# nested class in method
```java
class Electrocar{  
    private int id;  
  
    private class Engine{  
        public void startEngine(){}  
    }  
    public static class Battery{}  
    public void start(){  
        Engine engine = new Engine();  
        engine.startEngine();

		class SomeClass{
			
		}
		SomeClass smc = new SomeClass();
    }  
}  
```

Имеет доступ к приватным полям Electrocar и final данным метода.