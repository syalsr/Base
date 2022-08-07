# Equals HashCode Java
Эти методы нужно переопределять для корректного использования пользовательских объектов в коллекциях, чтобы не возникало ситуации, когда у объектов значения полей одинаковые, но они считаются, как разные, поскольку имеют разные адреса в памяти, тоже верно и для вычисления хеша.

```java
class Person{  
    String name;  
    int age;  
  
    @Override  
    public boolean equals(Object o) {  
        if (this == o) return true;  
        if (o == null || getClass() != o.getClass()) return false;  
        Person person = (Person) o;  
        return age == person.age && Objects.equals(name, person.name);  
    }  
  
    @Override  
    public int hashCode() {  
        return Objects.hash(name, age);  
    }  
}
```

