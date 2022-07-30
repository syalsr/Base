# Files Java
## Read
```java
String separator = File.separator;//у каждой OS свой разделитель
String path = "D:"+separator + "06" + separator + "test" + separator + "test.txt";  
File file = new File(path);  
Scanner scanner = new Scanner(file);  
while(scanner.hasNextLine()){  
    System.out.println(scanner.nextLine());  
}  
123  
123  
432  
543  
654  
scanner.close();
```

## Write
```java
File file = new File(path);  
PrintWriter pw = new PrintWriter(file);  
pw.println("Test1");  
pw.println("Test2");  
//произошла перезапись файла  
pw.close();
```

# Сериализация/Десириализация
```java
public class main {  
    public static void main(String[] args) {  
        serializable();  
        deserializable();  
        1: Bob  
        2: Dad  
    }  
    public static void serializable(){  
        Person person1 = new Person(1, "Bob");  
        Person person2 = new Person(2, "Dad");  
        try{  
            FileOutputStream fos = new FileOutputStream("people.bin");//работает с байтами  
            ObjectOutputStream oos = new ObjectOutputStream(fos);//работает с конкретными объектами  
  
            oos.writeObject(person1);  
            oos.writeObject(person2);  
  
            oos.close();  
        }catch (IOException e){  
            e.printStackTrace();  
        }  
    }  
    public static void deserializable(){  
        try{  
            FileInputStream fis = new FileInputStream("people.bin");//работает с байтами  
            ObjectInputStream ois = new ObjectInputStream(fis);//работает с конкретными объектами  
  
            Person person1 = (Person) ois.readObject();  
            Person person2 = (Person) ois.readObject();  
  
            System.out.println(person1);  
            System.out.println(person2);  
  
            ois.close();  
        }catch (IOException e){  
            e.printStackTrace();  
        }catch (ClassNotFoundException e){  
            e.printStackTrace();  
        }  
    }  
}  
  
class Person implements Serializable{//даем понять, что разрешаем сериализовывать класс  
    private int id;  
    private String name;  
  
    Person(int id, String name){  
        this.id = id;  
        this.name = name;  
    }  
    public String toString(){  
        return id + ": " + name;  
    }  
}
```

## Массивов
```java
public class main {  
    public static void main(String[] args) {  
        serializable();  
        deserializable();  
        [1: Bob, 2: Tom, 3: Bon]  
    }  
    public static void serializable(){  
        Person[] persons = {new Person(1, "Bob"),  
                            new Person(2, "Tom"),  
                            new Person(3, "Bon")  
        };  
        try{  
            FileOutputStream fos = new FileOutputStream("people.bin");//работает с байтами  
            ObjectOutputStream oos = new ObjectOutputStream(fos);//работает с конкретными объектами  
  
            oos.writeInt(persons.length);  
            for(Person person : persons)  
                oos.writeObject(person);  
  
            oos.close();  
        }catch (IOException e){  
            e.printStackTrace();  
        }  
    }  
    public static void deserializable(){  
        try{  
            FileInputStream fis = new FileInputStream("people.bin");//работает с байтами  
            ObjectInputStream ois = new ObjectInputStream(fis);//работает с конкретными объектами  
  
            int personCount = ois.readInt();  
            Person[] persons = new Person[personCount];  
  
            for(int i = 0; i < personCount; ++i)  
                persons[i] = (Person) ois.readObject();  
  
            System.out.println(Arrays.toString(persons));  
  
            ois.close();  
        }catch (IOException e){  
            e.printStackTrace();  
        }catch (ClassNotFoundException e){  
            e.printStackTrace();  
        }  
    }  
}
```

## transient
Используем, когда не хотим сериализовывать некоторые данные

```java
class Person implements Serializable{//даем понять, что разрешаем сериализовывать класс  
    private int id;  
    private transient String name;  
  
    Person(int id, String name){  
        this.id = id;  
        this.name = name;  
    }  
    public String toString(){  
        return id + ": " + name;  
    }  
}
```

# Serial Version UID
Может случиться так, что с течением времени наш класс изменится, чтобы это понять при десириализации проверяется это UID