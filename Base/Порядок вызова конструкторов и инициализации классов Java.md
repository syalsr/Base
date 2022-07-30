# Порядок вызова конструкторов и инициализации классов Java
Сначала вызываются все статические [[Блоки инициализации Java|блоки]] в очередности от первого статического блока корневого предка и выше по цепочке иерархии до статических блоков самого класса.

Затем вызываются нестатические блоки инициализации корневого предка, конструктор корневого предка и так далее вплоть до нестатических блоков и конструктора самого класса.

> Parent static block(s) → Child static block(s) → Grandchild static block(s)
> 
> → Parent non-static block(s) → Parent constructor →
> 
> → Child non-static block(s) → Child constructor →
> 
> → Grandchild non-static block(s) → Grandchild constructor

Пример 1:

```java
public class MainClass {

    public static void main(String args[]) {
        System.out.println(TestClass.v);
        new TestClass().a();
    }

}

public class TestClass {

    public static String v = "Some val";

    {
        System.out.println("!!! Non-static initializer");
    }

    static {
        System.out.println("!!! Static initializer");
    }

    public void a() {
        System.out.println("!!! a() called");
    }

}
```

Результат выполнения:
```
!!! Static initializer
Some val
!!! Non-static initializer
!!! a() called
```

Пример 2:
```java

public class MainClass {

    public static void main(String args[]) {
        new TestClass().a();
    }

}

public class TestClass {

    public static String v = "Some val";

    {
        System.out.println("!!! Non-static initializer");
    }

    static {
        System.out.println("!!! Static initializer");
    }

    public void a() {
        System.out.println("!!! a() called");
    }

}
```

Результат выполнения:
```
!!! Static initializer
!!! Non-static initializer
!!! a() called
```