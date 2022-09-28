# Stream API
С его помощью можно удобно изменять [[Collections Java]]
```java
List<Integer> numbers = new ArrayList<>();  
for(int i = 0; i < 100; ++i){  
    numbers.add((int) (Math.random() * 1000));  
}  
for(var s : numbers)  
    System.out.print(s + " ");  
List<String> string = numbers.stream().filter((n) -> n % 2 == 0)  
                                      .map((n) -> "Number: " + n)  
                                      .toList();  
for(var s : string)  
    System.out.println(s);
```

`stream()` преобразует наши данные в поток, поэтому после работы с ним, мы должны обратно преобразовать его в коллекцию.

```java
List<String> strings = List.of("11", "43", "54");  
strings.stream()  
        .map(string -> string + string)  
        .map(Integer::valueOf)  
        .filter(value -> value % 2 == 0)
        .sorted()
        .skip(1)//пропускаем первый элемент
        .limit(2)//ограничиваем количество элементов, т.е. выведутся 2 элемента
        .forEach(System.out::println);
```

Чтобы выполнять действия над коллекциями, нужно вызвать функцию которая не возвращает `stream`. То есть, в примере выше `map, filter, sorted, skip, limit` возвращают `stream`, чтобы мы могли использовать конвейерный вызов методов и самый последний метод не возвращает ничего.

# MapReduce
Позволяет параллельно обрабатывать данные.
```java
Stream.of(  
                new Student(18, "Ivan"),  
                new Student(23, "Petr"),  
                new Student(34, "Vasya"),  
                new Student(45, "Sveta"),  
                new Student(20, "Katya"),  
                new Student(68, "Den"),  
                new Student(101, "Kira")  
        )
        .sequential()//обработка в одном потоке т.к. данных мало, можно вызывать .parallel() для распараллеливания
        .map(Student::getAge)  
        .reduce(Integer::max)  
        .ifPresent(System.out::println);
```

# Optional
```java
Optional<Student> maybeStudent = Stream.of(  
                new Student(18, "Ivan"),  
                new Student(23, "Petr"),  
                new Student(34, "Vasya"),  
                new Student(45, "Sveta"),  
                new Student(20, "Katya"),  
                new Student(68, "Den"),  
                new Student(101, "Kira")  
        )  
        .parallel()  
        .sequential()  
        .filter(student -> student.getAge() < 18)  
        .reduce(((student, student2) -> student.getAge() > student2.getAge() ? student : student2));  
  
maybeStudent.ifPresent(System.out::println);//не выыводит ничего
```

https://metanit.com/java/tutorial/10.1.php