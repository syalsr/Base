# Function Java
* [[Лямбда выражения Java]]

Метод `main()` — точка входа в программу. В приложении может быть несколько таких методов в разных публичных классах. Если метод отсутствует, то компиляция возможна, но при запуске будет получена ошибка _`Error: Main method not found`_.

```java
public static void main(String[] args) {}
```

В Java параметр метода – всегда копия объекта. Значит параметры передаются всегда по значению, просто это значение может быть указателем на кучу.
```java
public static void main(String[] args) {
    var o = new Object();
    var i = 10;
    method(o, i);
    System.out.println(i + " " + o); // 10 and java.lang.Object@....
}

public static void method(Object o, int i) {
    o = null;
    i = 1000;
}
```
