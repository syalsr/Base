# Iterator Java
Итератор это интерфейс с помощью которого можно удобно итерироваться по контейнеру, который имеет методы
```
iterator() возвращает итератор
hasNext() проверяем, есть ли элемент
next() берем элемент
remove() удаляем элемент
```

Соответственно, если мы хотим итерироваться по нашей коллекции, то должны переопределить эти методы.

```java
List<Integer> list = List.if(1,2,3,4,5);
Iterator<Integer> = list.iterator();
while(iterator.hasNext()){
	System.out.println(iterator.next());
}
```

```java
public class List<T> implements Iterable<T>{  
    private T[] objects;  
    private int size;  
  
    public List(int init){  
        this.objects = (T[]) new Object[init];  
    }  
  
    @Override  
    public Iterator<T> iterator(){  
        return new ListIterator();  
    }  
  
    private class ListIterator implements Iterator<T>{  
        private int currentIndex;  
        @Override  
        public boolean hasNext(){  
            return currentIndex < size;  
        }  
        @Override  
        public T next(){  
            return objects[currentIndex++];  
        }  
    }  
}
```

Также у интерфейса есть метод
```java
default void forEachRemaining(Consumer<? super E> action) {  
    Objects.requireNonNull(action);  
    while (hasNext())  
        action.accept(next());  
}
```

С помощью которого можно итерироваться, как мы делали это выше.

```java
list.iterator().forEachRemainig(System.out::prinln);
У стандартного листа для этого есть метод
list.forEach(System.out::prinln);
```