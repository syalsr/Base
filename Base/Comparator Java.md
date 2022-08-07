# Comparator Java
В классе `Collections` находятся методы для сортировки коллекций

```java
List<String> l = new ArrayList<>();

l.add...

Collections.sort(l);//объекты отсортируются в лексеграфическом порядке
```

Если нас не устраивает стандартный компаратор, то мы можем создать свой

```java
class StringLenghtComparator implements Comparator<String>{
	@Override
	public int compare(String o1, String o2){  
	    if(o1.length() > o2.length()) return 1;  
	    if(o1.length() < o2.length()) return -1;  
	    return 0;  
	}
}
```

Метод `compare` должен работать так:
```
o1 > o2 => return 1
o1 < 02 => return -1
o1 == o2 => return 0
```

```java
List<String> l = new ArrayList<>();

l.add...

Collections.sort(l, new StringLenghtComparator());//теперь сортируем объекты по длине слов
```

Также можно использовать анонимный класс.

```java
Collections.sort(l, new Comparator<String>() {  
    @Override  
    public int compare(String o1, String o2) {  
        if(o1.length() > o2.length()) return 1;  
        if(o1.length() < o2.length()) return -1;  
        return 0;  
    }  
});
```

Либо [[Лямбда выражения Java]]

```java
Collections.sort(l, (String o1, String o2)->{  
    if(o1.length() > o2.length()) return 1;  
    if(o1.length() < o2.length()) return -1;  
    return 0;  
});
```

Либо, если мы оперируем пользовательскими данными.
```java
List<Person> l = new ArrayList<>();
l.sort(Comparator.comparing(Persoт::getFirstName).thenComparing(Person::getLastName));
```

# Компаратор для собственных типов
```java
class Person implements Comparable<Person>{
	...
	@Override
	public int compareTo(Person o){  
	    /*if(this.id > o.getId()) return 1;
	    if(this.id < o.getId()) return -1;
	    return 0;*/
	    return Integer.compare(this.id, o.id);
	}
}
```

Теперь нам не нужно создавать дополнительного класса компаратора или лямбда выражение. Теперь можем добавлять наши объекты Person в [[TreeSet Java]] и  они будут отсортированы.

